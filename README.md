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
