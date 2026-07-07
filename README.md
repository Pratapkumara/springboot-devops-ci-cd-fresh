# 🚀 DevOps Spring Boot Docker CI/CD Project

## 📌 Project Overview
This is a simple Spring Boot application containerized using Docker and designed for CI/CD pipeline practice.  
It demonstrates end-to-end DevOps workflow from build to deployment.

---

## 🛠️ Tech Stack
- Java 21
- Spring Boot
- Maven
- Docker
- Jenkins (CI/CD ready)
- Git & GitHub

---

## 📁 Project Structure

---

## 🚀 How to Run Locally

### 1. Build JAR
```bash
mvn clean package
docker build -t devops-app:1.0 .
docker run -d -p 8080:8080 --name devops-app devops-app:1.0
http://localhost:8080
DevOps App is Running 🚀
CI/CD Flow (Planned)

GitHub → Jenkins → Maven Build → Docker Build → Deploy Container

Future Enhancements
Jenkins Pipeline automation
Docker Compose setup
Prometheus + Grafana monitoring
Kubernetes deployment
Webhook test
Webhook Auto Build Test
