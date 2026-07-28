# 🚀 Jenkins CI/CD Pipeline with Docker & AWS EC2

This project demonstrates an end-to-end CI/CD pipeline using **Jenkins**, **Docker**, **Docker Hub**, and **AWS EC2**.

A simple Flask application is containerized using Docker. Jenkins automatically builds, versions, publishes, and deploys the application whenever changes are pushed to GitHub.

---

## 📌 Tech Stack

- Python (Flask)
- Docker
- Jenkins
- Docker Hub
- AWS EC2 (Ubuntu)
- Docker Compose
- Git & GitHub

---

# 🏗 Architecture

```
GitHub
   │
   ▼
Jenkins Pipeline
   │
   ├── Build Docker Image
   │
   ├── Tag Image
   │      ├── latest
   │      └── BUILD_NUMBER
   │
   ├── Push to Docker Hub
   │
   └── SSH into EC2
          │
          ▼
Docker Compose
          │
          ▼
Updated Flask Application
```

---

# ⚙ Pipeline Workflow

1. Pull source code from GitHub.
2. Build the Docker image.
3. Tag the image using:
   - `latest`
   - Jenkins `BUILD_NUMBER`
4. Push the image to Docker Hub.
5. Connect securely to the EC2 instance using SSH.
6. Pull the latest image using Docker Compose.
7. Restart the running container automatically.

---

# 📂 Project Structure

```
.
├── app.py
├── Dockerfile
├── docker-compose.yml
├── jenkinsfile
├── requirements.txt
├── templates/
└── static/
```

---

# 🔧 Jenkins Setup

## 1. Install Docker

```bash
sudo apt update
sudo apt install docker.io -y
```

---

## 2. Run Jenkins inside Docker

```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --group-add $(stat -c '%g' /var/run/docker.sock) \
  rohit-jenkins
```

The Docker socket is mounted so Jenkins can communicate with the Docker daemon running on the host.

---

## 3. Install Docker inside the Jenkins container

If you're using the official Jenkins Docker image, Docker CLI is not installed by default.

Example Dockerfile:

```dockerfile
FROM jenkins/jenkins:lts

USER root

RUN apt-get update && \
    apt-get install -y docker.io && \
    apt-get clean

USER jenkins
```

---

## 4. Give Jenkins access to Docker

If Jenkins runs inside a container, add the Docker socket group to the container when starting it.

```bash
--group-add $(stat -c '%g' /var/run/docker.sock)
```

This allows Jenkins to execute Docker commands without permission issues.

---

## 5. Install Required Jenkins Plugins

- Docker Pipeline
- Docker
- SSH Agent
- Git
- Pipeline
- Credentials Binding

---

## 6. Configure Credentials

### Docker Hub

Kind: Username with Password

```
ID: DOCKER
```

---

### SSH Credentials

Kind: SSH Username with Private Key

```
ID: EC2_JENKINS
```

---

# 🚀 Deployment

The EC2 server contains a simple `docker-compose.yml`.

```yaml
services:
  gallery:
    image: rohitsingh3200/gallery-app:latest
    container_name: gallery
    ports:
      - "5000:5000"
    restart: unless-stopped
```

Deployment is performed through:

```bash
docker compose pull
docker compose up -d
```

---

# 📦 Docker Image Versioning

Each successful build creates two image tags.

```
gallery-app:latest
gallery-app:<BUILD_NUMBER>
```

Example:

```
gallery-app:1
gallery-app:2
gallery-app:3
gallery-app:latest
```

This makes rollbacks simple if a deployment fails.

---

# 📸 Features

- Declarative Jenkins Pipeline
- Dockerized Flask application
- Automated Docker image builds
- Docker Hub integration
- Secure SSH deployment
- Docker Compose deployment
- Automatic image versioning
- Automated application updates

---

# 📚 What I Learned

Through this project I gained hands-on experience with:

- Jenkins Pipelines
- Docker image versioning
- Docker Hub authentication
- Jenkins Credentials
- SSH Agent plugin
- Docker Compose deployments
- Automated CI/CD workflows
- Troubleshooting Jenkins plugins and Docker permissions

---

# 🔮 Future Improvements

- Jenkins Shared Libraries
- Webhook-based automatic builds
- Blue-Green Deployment
- Terraform infrastructure provisioning
- Kubernetes deployment
- Monitoring with Prometheus & Grafana

---

## 👨‍💻 Author

**Rohit Singh**

GitHub: https://github.com/rohit-singh-3200

LinkedIn: https://www.linkedin.com/in/rohitkpsingh
