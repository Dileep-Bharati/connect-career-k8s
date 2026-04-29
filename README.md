# 🚀 Kubernetes Zero-to-Hero: Building a Self-Healing Web App

Welcome to the infrastructure repository for the **Connect Career Portal**. 

This repository serves as a hands-on, step-by-step masterclass in modern DevOps architecture. By following this guide, you will deploy a custom, containerized web application onto a local Kubernetes cluster. You will learn how to write Infrastructure as Code (IaC), containerize applications, and witness Kubernetes' self-healing and horizontal scaling in real-time.

## 🧠 Core Concepts Demonstrated
* **Containerization:** Packaging custom HTML/CSS into a Docker image using a custom `Dockerfile`.
* **Infrastructure as Code (IaC):** Writing declarative YAML blueprints for deployments and networking.
* **Self-Healing Architecture:** Watching the Control Plane automatically replace destroyed pods.
* **Zero-Downtime Rolling Updates:** Upgrading the application version without dropping a single user connection.

---

## 🛠️ Prerequisites
Before starting, ensure your system (Ubuntu/Linux recommended) has the following installed:
* [Docker](https://docs.docker.com/engine/install/) (Running and accessible)
* [Minikube](https://minikube.sigs.k8s.io/docs/start/)
* [Kubectl](https://kubernetes.io/docs/tasks/tools/)

---

## 🏗️ Step-by-Step Build Guide

### Step 1: Start the Local Cluster
First, boot up the local Kubernetes cluster. Think of this as powering on the data center that will host our application.
```bash
minikube start

### Step 2: Write the Custom Application
Create a dedicated workspace folder and write the custom application code.

```bash
mkdir k8s-blueprints
cd k8s-blueprints
```

Create an `index.html` file. This represents the custom code written by our developers:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Connect App - Career Module</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; margin-top: 50px; background-color: #f4f4f9; }
        h1 { color: #2c3e50; }
        p { color: #34495e; font-size: 18px; }
    </style>
</head>
<body>
    <h1>Welcome to the Connect Career Portal!</h1>
    <p>Version 3.0 - This is my custom code running in Kubernetes.</p>
</body>
</html>
```

### Step 3: Containerize the Application (The Recipe)
We need to pack the custom code into a Docker image. Create a file named `Dockerfile`:
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

### Step 4: Build the Image inside Minikube
**⚠️ CRITICAL MINIKUBE STEP:** 
Your local laptop and Minikube have separate Docker engines. To ensure the Kubernetes cluster can find our custom image without downloading it from the public internet, we must connect our terminal to Minikube's internal Docker daemon:
```bash
eval $(minikube -p minikube docker-env)
```

Now, build the custom image:
```bash
docker build -t connect-career-app:v3 .
```

### Step 5: Define the Infrastructure (YAML Blueprints)
Create the Deployment blueprint (`career-deployment.yaml`) to dictate how many replicas of our application should run. Note the `imagePullPolicy: Never` directive, which forces Kubernetes to use our locally built image.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: connect-career-frontend
spec:
  replicas: 5
  selector:
    matchLabels:
      app: career-frontend
  template:
    metadata:
      labels:
        app: career-frontend
    spec:
      containers:
      - name: web-server
        image: connect-career-app:v3
        imagePullPolicy: Never
        ports:
        - containerPort: 80
```

Create the Service blueprint (`career-service.yaml`) to act as a load balancer and expose our internal pods to the outside world.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: connect-career-service
spec:
  type: NodePort
  selector:
    app: career-frontend
  ports:
    - port: 80
      targetPort: 80
```

### Step 6: Deploy to Kubernetes
Apply both blueprints to the cluster. This is where we hand our desired state to the Kubernetes Manager (`etcd`):
```bash
kubectl apply -f career-deployment.yaml
kubectl apply -f career-service.yaml
```

Verify the pods are spinning up:
```bash
kubectl get pods
```

### Step 7: Access the Application
Use Minikube's built-in tunneling feature to safely expose the NodePort Service directly to your local browser:
```bash
minikube service connect-career-service
```
This will automatically open your default web browser to the live, highly-available application!

---

## 🧪 Test the Architecture: Self-Healing

The true power of Kubernetes is its ability to maintain the desired state. Let's simulate a server failure:
1. List your running pods: `kubectl get pods`
2. Copy the name of one pod and delete it: `kubectl delete pod <pod-name>`
3. Immediately run `kubectl get pods` again.

You will see Kubernetes instantly detect the missing pod and spin up a brand new replacement to maintain the 5 replicas requested in our blueprint.
