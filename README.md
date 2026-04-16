# Kubernetes Example Voting Application

This repository contains Kubernetes manifest files to deploy a multi-tier voting application. The application allows users to vote between two options and view real-time results through a web interface.

## Architecture Overview

The application is built using a microservices architecture consisting of five main components:

1.  **Voting App**: A front-end Python web application built with Flask that allows users to cast votes.
2.  **Redis**: An in-memory data store that acts as a message queue to collect submitted votes.
3.  **Worker**: A .NET application that consumes votes from Redis and persists them into a PostgreSQL database.
4.  **Database (DB)**: A PostgreSQL database that provides persistent storage for the voting results.
5.  **Result App**: A Node.js web application that retrieves results from the database and displays them in real-time.

## Project Structure

The repository provides two methods for deploying the stack:

* **`deployments/`**: Contains standard Kubernetes `Deployment` and `Service` objects. This is the recommended method for production-like environments as it ensures scalability and high availability.
* **`pods/`**: Contains individual `Pod` and `Service` manifests. This method is intended for development testing or educational purposes to understand basic Kubernetes primitives.

## Service Configuration

The following network configurations are defined for the application:

| Service | Port | Target Port | NodePort | Description |
| :--- | :--- | :--- | :--- | :--- |
| `vote` | 8080 | 80 | 31000 | External access for voting |
| `result` | 8081 | 80 | 31001 | External access for results |
| `redis` | 6379 | 6379 | N/A | Internal queue |
| `db` | 5432 | 5432 | N/A | Internal database |

## Deployment Instructions

### Method 1: Using Deployments (Recommended)

1.  Apply the backend components (Database and Redis):
    `kubectl apply -f deployments/db-deployment.yml -f deployments/db-service.yml`
    `kubectl apply -f deployments/redis-deployment.yml -f deployments/redis-service.yml`

2.  Apply the application components (Vote, Result, and Worker):
    `kubectl apply -f deployments/vote-deployment.yml -f deployments/vote-service.yml`
    `kubectl apply -f deployments/result-deployment.yml -f deployments/result-service.yml`
    `kubectl apply -f deployments/worker-deployment.yml`

### Method 2: Using Individual Pods

1.  Apply all manifests in the `pods/` directory:
    `kubectl apply -f pods/`

## Usage

Once deployed, the application interfaces can be accessed via any node's IP address in your Kubernetes cluster:

* **Voting Interface**: `http://<Node-IP>:31000`
* **Results Interface**: `http://<Node-IP>:31001`