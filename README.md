# 🍽️ Zomato Web Application – DevSecOps CI/CD Project

A real-world **DevSecOps CI/CD pipeline** implementation for a Zomato-like web application using **Jenkins, Docker, SonarQube, Trivy, and AWS**.  
This project demonstrates how to automate build, quality checks, security scanning, containerization, and deployment using industry best practices.

---

## 🚀 Project Overview

This project automates the complete CI/CD lifecycle:
- Source code checkout from GitHub
- Static code analysis using **SonarQube**
- Dependency vulnerability scanning using **OWASP Dependency-Check**
- File system & Docker image scanning using **Trivy**
- Docker image build & push
- Application deployment using Docker

The pipeline ensures **code quality, security, and consistency** before deployment.

---

## 🛠️ Tech Stack

| Category | Tools |
|-------|------|
| Cloud | AWS (EC2) |
| CI/CD | Jenkins |
| Containerization | Docker |
| Code Quality | SonarQube |
| Security Scanning | Trivy, OWASP Dependency-Check |
| SCM | Git & GitHub |
| OS | Amazon Linux |

---

## 🏗️ Infrastructure Details

- **Cloud Provider:** AWS  
- **EC2 Instance Type:** t2.large  
- **Operating System:** Amazon Linux  
- **Ports Used:**  
  - Jenkins → `8080`  
  - SonarQube → `9000`  
  - Application → `3000`

---

## 🔄 CI/CD Pipeline Workflow

1. **Clean Workspace**
2. **Source Code Checkout**
3. **SonarQube Code Analysis**
4. **Install Application Dependencies**
5. **OWASP Dependency Vulnerability Scan**
6. **Docker Image Build**
7. **Trivy File System Scan**
8. **Trivy Docker Image Scan**
9. **Docker Image Push**
10. **Application Deployment**

---

## 📄 Jenkins Pipeline (Declarative)

```groovy
pipeline {
    agent any

    tools {
        jdk 'jdk'
        nodejs 'nodejs'
    }

    environment {
        SCANNER_HOME = tool 'sonar'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git 'https://github.com/sonugupta4166/zomato-project1.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh """
                    ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=zomato \
                    -Dsonar.projectKey=zomato
                    """
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./',
                odcInstallation: 'dp-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t container436/zomato:v1 .'
            }
        }

        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs . --severity HIGH,CRITICAL --exit-code 0'
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image container436/zomato:v1 --severity HIGH,CRITICAL'
            }
        }

        stage('Docker Push') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub-creds') {
                    sh 'docker push container436/zomato:v1'
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker rm -f zomato || true
                docker run -d --name zomato -p 3000:3000 container436/zomato:v1
                '''
            }
        }
    }
}
🔐 Security Practices Implemented
No hardcoded credentials

Jenkins secrets stored securely

Static code analysis using SonarQube

Dependency vulnerability scanning

Container image security scanning

Secure Docker image handling

✅ Verification
Jenkins pipeline executed successfully

SonarQube quality gate passed

Trivy scan completed

Application accessible via browser

📚 Key Learnings
End-to-end DevSecOps CI/CD automation

Integrating security into CI/CD pipelines

Docker-based deployment workflows

Real-world Jenkins pipeline design

Cloud-based DevOps implementation

🔮 Future Enhancements
Kubernetes deployment

GitHub Webhooks integration

Blue-Green or Canary deployment

Monitoring using Prometheus & Grafana

Ingress & LoadBalancer setup

👨‍💻 Author
Sonu Kumar
DevOps Engineer (Fresher)
🔗 LinkedIn: https://www.linkedin.com/in/sonukumar4166/
🔗 GitHub: https://github.com/sonugupta4166
