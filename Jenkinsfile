pipeline {

    agent any

    environment {

        // Docker Hub username
        DOCKER_HUB_USER = 'dhanrajchavhan348-web'

        // Docker image/application name
        APP_NAME = 'three-tier-backend'

        // Jenkins automatically generates build number
        IMAGE_TAG = "${BUILD_NUMBER}"

        // AWS EC2 public IP
        AWS_INSTANCE_IP = 'YOUR_EC2_PUBLIC_IP'

        // Docker Hub credentials stored in Jenkins
        DOCKER_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {

        stage('Git Checkout') {
            steps {
                echo 'Checking out source code from GitHub...'
                checkout scm
            }
        }

        stage('Maven Compile & Test') {
            steps {
                echo 'Running Maven tests...'
                sh 'mvn clean test'
            }
        }

        stage('SonarQube Analysis') {
            steps {

                echo 'Running SonarQube analysis...'

                withSonarQubeEnv('SonarQube-Server') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {
            steps {

                echo 'Checking SonarQube Quality Gate...'

                timeout(time: 10, unit: 'MINUTES') {

                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Maven Package') {
            steps {

                echo 'Creating application JAR/WAR...'

                sh 'mvn package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {

                echo 'Building Docker image...'

                sh """
                    docker build \
                    -t ${DOCKER_HUB_USER}/${APP_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Docker Login & Push') {
            steps {

                echo 'Logging into Docker Hub...'

                sh '''
                    echo "$DOCKER_CREDENTIALS_PSW" | \
                    docker login \
                    -u "$DOCKER_CREDENTIALS_USR" \
                    --password-stdin
                '''

                echo 'Pushing Docker image to Docker Hub...'

                sh """
                    docker push \
                    ${DOCKER_HUB_USER}/${APP_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to AWS EC2') {
            steps {

                echo 'Deploying application to AWS EC2...'

                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'aws-ec2-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {

                    sh """

                        ssh \
                        -o StrictHostKeyChecking=no \
                        -i ${SSH_KEY} \
                        ${SSH_USER}@${AWS_INSTANCE_IP} '

                            echo "Pulling latest Docker image..."

                            docker pull \
                            ${DOCKER_HUB_USER}/${APP_NAME}:${IMAGE_TAG}

                            echo "Starting application..."

                            cd /home/${SSH_USER}/app

                            export IMAGE_TAG=${IMAGE_TAG}

                            docker compose up -d --force-recreate

                            echo "Deployment completed."

                            docker ps
                        '
                    """
                }
            }
        }
    }

    post {

        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed!'
        }

        always {
            cleanWs()
        }
    }
}
