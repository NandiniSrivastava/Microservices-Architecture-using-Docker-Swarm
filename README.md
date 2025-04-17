# Microservices Architecture with Docker Swarm ⚓

This guide explains how to deploy a microservices architecture using Docker Swarm with an API Gateway and a Backend Service.

<p align="center">
  <img src="https://github.com/TarakKatoch/My-Docker-Dockyard/raw/6aa77085daae451d6bd1df2cc3d12c78998797b3/Microservices%20Architecture%20using%20Docker%20Swarm/assets/docker-to-swarm-1.png" alt="Docker to Swarm" width="300" />
</p>


## 🚀 Step 1: Install Docker & Initialize Docker Swarm

### 1.1 Install Docker
Ensure Docker is installed on your machine. Verify with:
```sh
docker --version
```
If not installed, download it from Docker's official site and install it.

**Note:** Docker Desktop should be running in the background for Docker Swarm to work properly.

### 1.2 Initialize Docker Swarm
Start Docker Swarm:
```sh
docker swarm init
```
This makes your machine the Swarm Manager.

  ![image](https://github.com/user-attachments/assets/dfeaa60a-5cac-4a64-9b8f-2ee241806a1d)

## 📁 Project Structure
```
microservices-docker-swarm/
│── backend-service/
│   ├── backend.py
│   ├── Dockerfile
│
│── api-gateway/
│   ├── api_gateway.py
│   ├── Dockerfile
│
│── docker-compose.yml
│── README.md
```

## 🛠 Step 2: Create the Microservices Code

### Backend Service
Create `backend.py`:
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "rajput_tarakk"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000)
```
Create `Dockerfile`:
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY backend.py /app
RUN pip install flask
CMD ["python", "backend.py"]
```

### API Gateway Service
Create `api_gateway.py`:
```python
from flask import Flask
import requests

app = Flask(__name__)

@app.route('/')
def hello():
    backend_response = requests.get('http://backend-service:5000')
    return f"API Gateway: {backend_response.text}"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=8080)
```
Create `Dockerfile`:
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY api_gateway.py /app
RUN pip install flask requests
CMD ["python", "api_gateway.py"]
```

## 📦 Step 3: Build Docker Images
```sh
docker build -t backend-service ./backend-service
```
```sh
docker build -t api-gateway ./api-gateway
```
```sh
docker images
```
![image](https://github.com/user-attachments/assets/d6d6f9ba-d791-4e51-98f2-88369c00769a)

## 📜 Step 4: Create Docker Compose File for Swarm
Create `docker-compose.yml`:
```yaml
version: '3.7'

services:
  backend-service:
    image: backend-service
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    networks:
      - app-network
    ports:
      - "5000:5000"

  api-gateway:
    image: api-gateway
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    networks:
      - app-network
    ports:
      - "8080:8080"
    depends_on:
      - backend-service

networks:
  app-network:
    driver: overlay
```

## 🚀 Step 5: Deploy the Microservices to Docker Swarm
```sh
docker stack deploy -c docker-compose.yml my_microservices
```

  ![image](https://github.com/user-attachments/assets/77dfb7c5-391b-400e-a924-1baf5df70d23)


## 📊 Step 6: Verify the Deployment
```sh
docker stack services my_microservices
```
  ![image](https://github.com/user-attachments/assets/7257c8e6-00ae-4873-9853-64c19034df4c)

```sh
docker ps
```
  ![image](https://github.com/user-attachments/assets/4378438f-dada-4796-ae25-10a960b612de)

## 🌐 Step 7: Access the Microservices
Open your browser and go to:
```sh
http://localhost:8080
```
You should see: **API Gateway: NANDINI_SRIVASTAVA**

  ![image](https://github.com/user-attachments/assets/add6a367-0685-45c7-becb-d1e16e2ee5c4)

## 🔄 Step 8: Scaling the Services
```sh
docker service scale my_microservices_backend-service=5
```
![image](https://github.com/user-attachments/assets/f34562b4-795b-4c80-9b81-1500a76036d4)

```sh
docker stack services my_microservices
```
![image](https://github.com/user-attachments/assets/9b0e94e5-1339-431e-85ac-699ac96facc2)


## 🛠 Step 9: Updating the Services
Rebuild and update the backend service:
```sh
docker build -t backend-service ./backend-service
```
![image](https://github.com/user-attachments/assets/3f5e0992-45cb-477a-88be-4ebb4bc4f220)

```sh
docker service update --image backend-service:latest my_microservices_backend-service
```
![image](https://github.com/user-attachments/assets/a16672c1-8a52-4b76-89cb-44c67b0a0ee7)

## 🛑 Step 10: Remove the Stack
```sh
docker stack rm my_microservices
```
![image](https://github.com/user-attachments/assets/02cc838b-c192-4ccd-a44e-c9c0353ed8ed)

```sh
docker swarm leave --force
```

---
This guide helps you deploy a microservices architecture using Docker Swarm. 🚀
