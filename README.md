# NGINX-Hello-World-CICD

This is a custom CICD project, for automated build and deployment of a simple nginx web app on kubernetes.

## Prerequisites and dependencies:

 - A host (Linux/Windows) with pre-installed Jenkins, Docker and any CD application (in our case Ubuntu EC2 instance + ArgoCD) 
 - A clean kubernetes cluster + deployed ingress controller and metrics-server (minikube does the job just fine)
 - A custom built docker image with docker and git to server as and Jenkins Agent for the Jenkins pipeline
