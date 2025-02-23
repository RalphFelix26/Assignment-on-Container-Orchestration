# Assignment-on-Container-Orchestration
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

helm version
If Helm is not recognized, add it to your system PATH and restart the terminal.

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
Step 6: Verify Deployment
Check if the Helm release is installed:

sh

helm list
Check Kubernetes pods:

sh

kubectl get pods
Check Kubernetes services:

sh

kubectl get services
If you see mern-app running, the deployment was successful!

Troubleshooting
Helm Install Fails: "nil pointer evaluating interface {}.port"
Fix: Ensure values.yaml has service.port defined for each service.

Helm Command Not Found
Fix: Ensure Helm is installed and added to your system PATH.

Kubernetes Cluster Not Running
If kubectl get pods gives a connection error:

sh

kubectl cluster-info
minikube start  # If using Minikube

Useful Commands

Delete Helm Deployment
sh


helm uninstall mern-app

Delete All Kubernetes Resources
sh


kubectl delete all --all

Debug Kubernetes Pod
sh


kubectl logs <pod_name>
kubectl describe pod <pod_name>

Next Steps
Scale the deployment using Helm
Add environment variables via Helm values
Deploy on a cloud provider (AWS EKS, GKE, AKS)

Your MERN App is Running on Kubernetes! 

