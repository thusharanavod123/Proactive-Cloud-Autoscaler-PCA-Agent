

 Proactive Cloud Autoscaler (PCA) Agent

💡 Overview

The Proactive Cloud Autoscaler (PCA) Agent is a high-performance MLOps prototype designed to replace reactive auto-scaling policies. It uses a trained TensorFlow Time-Series Forecasting Model to predict the system's resource needs (e.g., CPU utilization) up to one hour into the future.

The system is deployed as a lean, compiled Go microservice that acts as the low-latency API endpoint, making it ideal for integration with Kubernetes Horizontal Pod Autoscalers (HPA) or specialized cloud infrastructure (e.g., Google Cloud Compute, Vertex AI).

🛠️ Technology Stack

Layer

Tool / Technology

Purpose in This Project

Model Development

TensorFlow / Python

Used in the Colab notebook to train the forecasting model (Time-Series Regression).

Serving API

Go (Golang)

Used to build the ultra-fast, concurrent API microservice (main.go) that exposes the prediction.

Deployment

Docker

Used to containerize the Go binary for portable, secure, and fast deployment to any cloud environment.

API Protocol

HTTP / gRPC (Conceptual)

The Go service acts as the gateway to the model, ensuring low-latency communication.

🎯 Optimization Goal

This project solves the "reactive scaling" problem:

Cost Savings: By predicting low demand, the agent can trigger scale-down operations earlier, reducing spending on idle compute.

Latency Prevention: By predicting a traffic spike 30-60 minutes in advance, the agent proactively scales up resources, preventing user-facing latency and system outages.

💻 Project Structure

The repository is structured to separate the training code (for data scientists) from the serving code (for DevOps engineers).

File/Folder

Type

Purpose

main.go

Go Code

The main HTTP/API server, including validation logic.

Dockerfile

Container Blueprint

Defines the multi-stage build for a tiny Go server image.

test_prediction.http

API Test Script

Used by developers (e.g., in GoLand/VS Code) to test API latency and functionality.

README.md

Documentation

This file (explaining everything!).

[Model-Artifact]/

Placeholder

Directory for the exported TensorFlow SavedModel (not committed to Git).

⚙️ Quick Start (Running the API Locally)

You must have Docker installed to run this service.

1. Build the Docker Image

# Builds the image using the multi-stage Dockerfile
docker build -t pca-agent:latest .


2. Run the Container

# Runs the container and maps the internal port 8080 to your host machine's 8080
docker run -d -p 8080:8080 --name pca-service pca-agent:latest


3. Test the Endpoint

Run the requests inside the test_prediction.http file or use curl:

curl -X POST http://localhost:8080/predict -H "Content-Type: application/json" -d '{
  "cpu_history": [70.0, 72.0, 75.0, 77.0, 78.0, 80.0, 81.0, 82.5, 84.0, 85.0]
}'


Expected Output

{
  "predicted_cpu_usage": 89.25,
  "latency_ms": 1 
}


The latency should be extremely low, confirming the Go service is efficient.
