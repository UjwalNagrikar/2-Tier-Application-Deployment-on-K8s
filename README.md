# 🚀 Two-Tier Flask MySQL Application

A production-ready two-tier web application built with Flask and MySQL, containerized with Docker and deployable on Kubernetes.

![Python](https://img.shields.io/badge/Python-3.9-blue)
![Flask](https://img.shields.io/badge/Flask-2.0.1-green)
![MySQL](https://img.shields.io/badge/MySQL-5.7-orange)
![Docker](https://img.shields.io/badge/Docker-Enabled-blue)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Ready-326CE5)

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Setup Instructions](#setup-instructions)
  - [Local Development with Docker Compose](#local-development-with-docker-compose)
  - [Kubernetes Deployment](#kubernetes-deployment)
- [Common Issues & Solutions](#common-issues--solutions)
- [Database Access](#database-access)
- [API Endpoints](#api-endpoints)
- [Contributing](#contributing)

## 🎯 Overview

This is a simple yet robust two-tier web application that demonstrates the integration of a Flask backend with a MySQL database. Users can submit messages through a modern web interface, which are then stored persistently in MySQL.

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Kubernetes Cluster                        │
│                                                               │
│  ┌──────────────────┐              ┌──────────────────┐    │
│  │   Flask App      │              │   MySQL 5.7      │    │
│  │   (Frontend +    │─────────────▶│   Database       │    │
│  │   Backend)       │              │                  │    │
│  │                  │              │                  │    │
│  │  Port: 5000      │              │  Port: 3306      │    │
│  └──────────────────┘              └──────────────────┘    │
│         │                                    │              │
│         │                                    │              │
│    NodePort Service                  PersistentVolume       │
│    (Port: 30004)                     (256Mi Storage)        │
│         │                                                    │
└─────────┼────────────────────────────────────────────────────┘
          │
          ▼
    External Access
  (http://localhost:30004)
```

### Component Breakdown

- **Tier 1 (Frontend/Backend)**: Flask application serving both UI and API endpoints
- **Tier 2 (Database)**: MySQL 5.7 database with persistent storage
- **Communication**: Internal cluster DNS resolution between services
- **Storage**: PersistentVolume for MySQL data persistence

## 💻 Tech Stack

### Backend
- **Python 3.9**: Core programming language
- **Flask 2.0.1**: Lightweight web framework
- **Flask-MySQLdb**: MySQL database adapter

### Database
- **MySQL 5.7**: Relational database management system

### DevOps & Infrastructure
- **Docker**: Containerization platform
- **Docker Compose**: Multi-container orchestration
- **Kubernetes**: Container orchestration for production
- **Multi-stage Dockerfile**: Optimized image builds

### Frontend
- **HTML5/CSS3**: Modern web standards
- **JavaScript (jQuery)**: AJAX interactions
- **Glassmorphism Design**: Modern UI aesthetics

## ✨ Features

- ✅ Real-time message submission without page reload (AJAX)
- ✅ Persistent data storage with MySQL
- ✅ Responsive and modern UI with glassmorphic design
- ✅ Health checks for both Flask and MySQL containers
- ✅ Production-ready Docker multi-stage builds
- ✅ Kubernetes deployment with persistent volumes
- ✅ Automatic database initialization
- ✅ Container orchestration with Docker Compose
- ✅ Cross-platform Makefile support (Linux/macOS/Windows)

## 📦 Prerequisites

### For Docker Compose Deployment
- Docker Engine 20.10+
- Docker Compose 1.29+
- Make (optional, for using Makefile)

### For Kubernetes Deployment
- Kubernetes cluster (minikube, kind, or cloud provider)
- kubectl CLI tool
- Docker Hub account (for custom images)

### For Local Development
- Python 3.9+
- MySQL 5.7+
- pip package manager

## 📁 Project Structure

```
two-tier-flask-app/
│
├── app.py                      # Flask application entry point
├── requirements.txt            # Python dependencies
├── Dockerfile                  # Standard Docker image
├── Dockerfile-multistage       # Optimized multi-stage build
├── docker-compose.yml          # Docker Compose configuration
├── Makefile                    # Build automation
├── message.sql                 # Database initialization script
├── .gitignore                  # Git ignore rules
│
├── templates/
│   └── index.html              # Frontend UI template
│
├── k8s/
│   └── singl-deployment.yml    # Kubernetes deployment manifests
│
└── mysql-data/                 # MySQL persistent data (ignored by git)
```

## 🚀 Setup Instructions

### Local Development with Docker Compose

#### Step 1: Clone the Repository

```bash
git clone <repository-url>
cd two-tier-flask-app
```

#### Step 2: Set Environment Variables (Linux/macOS)

```bash
export UID=$(id -u)
export GID=$(id -g)
```

For Windows PowerShell:
```powershell
$env:UID = 1000
$env:GID = 1000
```

#### Step 3: Build and Run with Docker Compose

**Using Makefile (Recommended):**

```bash
# Build the Docker images
make build

# Start the application
make run

# Stop the application
make stop

# Clean up containers and images
make clean
```

**Using Docker Compose directly:**

```bash
# Build and start services
docker-compose up -d --build

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

#### Step 4: Access the Application

Open your browser and navigate to:
```
http://localhost:5000
```

### Kubernetes Deployment

#### Step 1: Ensure Kubernetes Cluster is Running

```bash
# For minikube
minikube start

# Verify cluster
kubectl cluster-info
kubectl get nodes
```

#### Step 2: Update the PersistentVolume Path (Important!)

Edit `k8s/singl-deployment.yml` and update the `hostPath` in the PersistentVolume section to match your local system:

```yaml
hostPath:
  # Linux/macOS example
  path: /path/to/your/two-tier-flask-app/mysqldata
  
  # Windows example
  path: /c/Users/YourUsername/path/to/mysqldata
```

#### Step 3: Create the mysqldata Directory

```bash
# Linux/macOS
mkdir -p mysqldata

# Windows
mkdir mysqldata
```

#### Step 4: Deploy to Kubernetes

```bash
# Apply all configurations
kubectl apply -f k8s/singl-deployment.yml

# Verify deployments
kubectl get pods
kubectl get services
kubectl get pv,pvc
```

#### Step 5: Wait for Pods to be Ready

```bash
# Watch pod status
kubectl get pods -w

# Check pod logs if needed
kubectl logs -f <pod-name>
```

Expected output:
```
NAME                            READY   STATUS    RESTARTS   AGE
mysql-xxxxxxxxxx-xxxxx          1/1     Running   0          2m
two-tier-app-xxxxxxxxxx-xxxxx   1/1     Running   0          2m
```

#### Step 6: Access the Application

**For Minikube:**
```bash
minikube service two-tier-app-service
```

**Or access directly via NodePort:**
```
http://localhost:30004
http://<node-ip>:30004
```

**Get Node IP:**
```bash
kubectl get nodes -o wide
```

## 🐛 Common Issues & Solutions

### Issue 1: MySQL Pod CrashLoopBackOff

**Problem**: MySQL container fails to start due to permission issues.

**Solution**:
```bash
# Fix permissions on mysql-data directory
sudo chown -R 999:999 mysql-data/

# Or delete and recreate
sudo rm -rf mysql-data/
mkdir mysql-data
```

### Issue 2: PersistentVolume Not Binding

**Problem**: PVC remains in "Pending" state.

**Solution**:
- Ensure the `hostPath` in PV matches your actual directory
- Check PV access mode matches PVC requirements
- Verify you have only one PV and one PVC

```bash
kubectl describe pv mysql-pv
kubectl describe pvc mysql-pvc
```

### Issue 3: Flask Cannot Connect to MySQL

**Problem**: Flask app shows database connection errors.

**Solution**:
- Wait for MySQL to be fully ready (check healthcheck)
- Verify environment variables in deployment
- Check service name resolution

```bash
# Check MySQL service
kubectl get svc mysql

# Test connection from Flask pod
kubectl exec -it <flask-pod-name> -- ping mysql
```

### Issue 4: Port Already in Use (Docker Compose)

**Problem**: Port 5000 or 3306 already in use.

**Solution**:
```bash
# Find process using the port
lsof -i :5000
lsof -i :3306

# Kill the process
kill -9 <PID>

# Or change the port in docker-compose.yml
```

### Issue 5: Image Pull Errors in Kubernetes

**Problem**: Cannot pull Docker image from Docker Hub.

**Solution**:
```bash
# Build and push your own image
docker build -t your-dockerhub-username/two-tier-flask-app:latest .
docker push your-dockerhub-username/two-tier-flask-app:latest

# Update image in k8s/singl-deployment.yml
```

## 🗄️ Database Access

### Access MySQL in Docker Compose

```bash
# Connect to MySQL container
docker exec -it mysql mysql -uroot -proot

# Or use admin user
docker exec -it mysql mysql -uadmin -padmin devops
```

### Access MySQL in Kubernetes

```bash
# List pods to get MySQL pod name
kubectl get pods

# Connect to MySQL
kubectl exec -it mysql-xxxxxxxxxx-xxxxx -- mysql -uadmin -padmin

# Or as root
kubectl exec -it mysql-xxxxxxxxxx-xxxxx -- mysql -uroot -padmin
```

### Useful MySQL Commands

```sql
-- Show all databases
SHOW DATABASES;

-- Use the application database
USE mydb;  -- or 'devops' for Docker Compose

-- Show tables
SHOW TABLES;

-- View all messages
SELECT * FROM messages;

-- Insert a test message
INSERT INTO messages (message) VALUES ('Test message from MySQL CLI');

-- Delete all messages
DELETE FROM messages;

-- Drop table
DROP TABLE messages;
```

## 🔌 API Endpoints

### GET /
- **Description**: Renders the main page with all messages
- **Response**: HTML page

### POST /submit
- **Description**: Submits a new message
- **Content-Type**: application/x-www-form-urlencoded
- **Parameters**: 
  - `new_message` (string): The message to store
- **Response**: JSON
  ```json
  {
    "message": "Your submitted message"
  }
  ```

### GET /health (for Kubernetes healthcheck)
- **Description**: Health check endpoint
- **Response**: HTTP 200 if healthy

## 🔧 Advanced Configuration

### Using Multi-Stage Dockerfile

For production deployments, use the optimized multi-stage build:

```bash
docker build -f Dockerfile-multistage -t your-app:latest .
```

Benefits:
- Smaller final image size
- Separate build and runtime dependencies
- Improved security with minimal runtime layers

### Scaling the Application

```bash
# Scale Flask app to 3 replicas
kubectl scale deployment two-tier-app --replicas=3

# Verify scaling
kubectl get pods
```

### Setting Resource Limits

Add to your deployment:

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "250m"
  limits:
    memory: "512Mi"
    cpu: "500m"
```

## 🧹 Cleanup

### Docker Compose Cleanup

```bash
# Stop and remove containers
docker-compose down

# Remove volumes as well
docker-compose down -v

# Remove images
docker rmi ujwalnagrikar/two-tier-flask-app:latest
```

### Kubernetes Cleanup

```bash
# Delete all resources
kubectl delete -f k8s/singl-deployment.yml

# Verify deletion
kubectl get all

# Clean up persistent volumes
kubectl delete pv mysql-pv
kubectl delete pvc mysql-pvc

# Remove data directory
rm -rf mysqldata/
```

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 👨‍💻 Author

**Ujwal Nagrikar**
- Docker Hub: [@ujwalnagrikar](https://hub.docker.com/u/ujwalnagrikar)

## 🙏 Acknowledgments

- Flask community for excellent documentation
- MySQL team for robust database engine
- Kubernetes community for orchestration tools

---

