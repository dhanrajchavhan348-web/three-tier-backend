# Three-Tier Backend Application – CI/CD Pipeline

## 📌 Project Overview

This project demonstrates an end-to-end **CI/CD pipeline** for a Java Spring Boot backend application.

The pipeline automates the process of taking source code from GitHub, testing it, analyzing code quality, building a Docker image, pushing the image to Docker Hub, and deploying the application to an AWS EC2 server.

## 🏗️ Architecture

```text
Developer
    |
    | Git Push
    v
GitHub
    |
    v
Jenkins
    |
    +----> Maven Build & Test
    |
    +----> SonarQube Code Analysis
    |
    +----> Quality Gate
    |
    +----> Docker Build
    |
    +----> Docker Hub
    |
    v
AWS EC2
    |
    +----> Docker Pull
    |
    +----> Docker Compose
    |
    v
Spring Boot Application
```

## 🛠️ Technologies Used

* **Git & GitHub** – Source code management
* **Jenkins** – CI/CD automation
* **Maven** – Build and dependency management
* **SonarQube** – Code quality analysis
* **Docker** – Application containerization
* **Docker Hub** – Container image registry
* **AWS EC2** – Application deployment
* **Docker Compose** – Container deployment and management
* **Java / Spring Boot** – Backend application

## 📂 Project Structure

```text
three-tier-backend/
│
├── Jenkinsfile
├── Dockerfile
├── docker-compose.yml
├── pom.xml
├── .dockerignore
├── .gitignore
├── README.md
│
└── src/
    ├── main/
    │   ├── java/
    │   │   └── com/
    │   │       └── example/
    │   │           └── backend/
    │   │               ├── BackendApplication.java
    │   │               └── HelloController.java
    │   │
    │   └── resources/
    │       └── application.properties
    │
    └── test/
        └── java/
            └── com/
                └── example/
                    └── backend/
                        └── BackendApplicationTests.java
```

## 🔄 CI/CD Pipeline

The Jenkins pipeline performs the following steps:

### 1. Git Checkout

Jenkins downloads the latest source code from GitHub.

### 2. Maven Build & Test

Maven compiles the application and runs unit tests.

```bash
mvn clean test
```

### 3. SonarQube Analysis

SonarQube analyzes the source code for code quality issues, bugs, vulnerabilities, and code smells.

### 4. Quality Gate

Jenkins checks the SonarQube Quality Gate.

If the quality gate fails, the pipeline stops.

### 5. Maven Package

The application is packaged as a JAR file.

```bash
mvn package -DskipTests
```

### 6. Docker Build

Jenkins creates a Docker image.

```bash
docker build -t USERNAME/three-tier-backend:BUILD_NUMBER .
```

### 7. Docker Hub Push

The Docker image is pushed to Docker Hub.

```bash
docker push USERNAME/three-tier-backend:BUILD_NUMBER
```

### 8. Deploy to AWS EC2

Jenkins connects to the AWS EC2 server using SSH.

The server pulls the Docker image and starts the application using Docker Compose.

```bash
docker pull USERNAME/three-tier-backend:BUILD_NUMBER

docker compose up -d --force-recreate
```

## 🐳 Docker

The application is packaged inside a Docker container.

The Docker image contains:

* Java runtime
* Spring Boot application
* Application dependencies

The container exposes port:

```text
8080
```

## ☁️ AWS Deployment

The application is deployed on an AWS EC2 instance.

The EC2 server runs:

```text
Docker
Docker Compose
Spring Boot Container
```

Application URL:

```text
http://YOUR_EC2_PUBLIC_IP:8080
```

## 🔐 Security

Sensitive information is not stored in this GitHub repository.

The following are stored securely in Jenkins Credentials:

* Docker Hub credentials
* AWS EC2 SSH private key
* SonarQube authentication token

Do not commit:

```text
*.pem
*.key
.env
passwords
API keys
access keys
secret keys
```

## 🚀 How to Run Locally

### Clone the repository

```bash
git clone https://github.com/dhanrajchavhan348-web/three-tier-backend.git
```

### Go to the project directory

```bash
cd three-tier-backend
```

### Run Maven tests

```bash
mvn clean test
```

### Build the application

```bash
mvn package -DskipTests
```

### Build Docker image

```bash
docker build -t three-tier-backend:test .
```

### Run Docker container

```bash
docker run -d -p 8080:8080 --name three-tier-backend three-tier-backend:test
```

### Access the application

```text
http://localhost:8080
```

## 📈 Benefits of the Project

* Automated build and testing
* Automated code quality checking
* Automated Docker image creation
* Automated Docker Hub deployment
* Automated AWS deployment
* Reduced manual deployment effort
* Consistent application deployments
* Faster software delivery

## 👨‍💻 Author

**DHANRAJ M.CHAVHAN**

Cloud & DevOps Enthusiast

### Skills Demonstrated

AWS | Linux | Git | GitHub | Jenkins | Maven | SonarQube | Docker | Docker Compose | CI/CD
