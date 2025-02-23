## Prerequisites
Ensure you have the following installed before proceeding:

- [Docker](https://www.docker.com/get-started)
- [Kubernetes (Minikube or Docker Desktop)](https://kubernetes.io/docs/setup/)
- [Helm](https://helm.sh/docs/intro/install/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/)



Step 1: Build and Push Docker Images   # Before deploying to Kubernetes, containerize the application:

Backend
Navigate to the backend directory and build the Docker image:
sh

cd learnerReportCS_backend
docker build -t mydockerhubusername/backend:latest .
docker push mydockerhubusername/backend:latest

Frontend  #Navigate to the frontend directory and build the Docker image:

sh

cd ../learnerReportCS_frontend
docker build -t mydockerhubusername/frontend:latest .
docker push mydockerhubusername/frontend:latest

Step 2: Setup Helm Chart
Navigate to the Helm chart directory:

sh

cd ../mern-chart
Ensure Helm is correctly installed:

sh

helm version #If Helm is not recognized, add it to your system PATH and restart the terminal.

Step 3: Update values.yaml  #Open mern-chart/values.yaml and define service.port for each component:

yaml

backend:
  image: mydockerhubusername/backend:latest
  replicas: 2
  service:
    port: 5000  # Backend service port

frontend:
  image: mydockerhubusername/frontend:latest
  replicas: 2
  service:
    port: 80  # Frontend service port

mongo:
  image: mongo:latest
  replicas: 1
  service:
    port: 27017  # MongoDB service port
    
Step 4: Update test-connection.yaml
Open mern-chart/templates/tests/test-connection.yaml and modify the args section:

yaml


args: ['{{ include "mern-chart.fullname" . }}:{{ .Values.backend.service.port }}']
or, depending on the service you want to test:

yaml

args: ['{{ include "mern-chart.fullname" . }}:{{ .Values.frontend.service.port }}']
or for MongoDB:

yaml


args: ['{{ include "mern-chart.fullname" . }}:{{ .Values.mongo.service.port }}']
Save the file.

Step 5: Install the Helm Chart  #From the mern-chart directory, install or upgrade the Helm release:

sh

helm upgrade --install mern-app ./mern-chart

Step 6: Verify Deployment #Check if the Helm release is installed:

sh

helm list
Check Kubernetes pods:

sh

kubectl get pods
Check Kubernetes services:

sh

kubectl get services # If you see mern-app running, the deployment was successful!

Step 7:Automate Deployment with Jenkins

Jenkins Groovy Code

pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
    }
    stages {
        stage('Build and Push Images') {
            steps {
                script {
                    docker.build("mydockerhubusername/frontend").push("latest")
                    docker.build("mydockerhubusername/backend").push("latest")
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
        stage('Deploy using Helm') {
            steps {
                sh 'helm upgrade --install mern-app ./helm/mern-chart'
            }
        }
    }
}


Step 8 : Testing and Validation  #Deploy to Minikube or a Kubernetes cluster

sh

kubectl apply -f backend-deployment.yaml
kubectl apply -f frontend-deployment.yaml
kubectl apply -f mongo-deployment.yaml

Check running pods and services:

sh

kubectl get pods
kubectl get services

Access the frontend:

sh

minikube service frontend-service

This approach ensures a structured deployment process using Kubernetes, Helm, and Jenkins to automate the CI/CD pipeline. 
