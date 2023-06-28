# CICD-Project

This is a custom CICD project for automated build and deployment of a simple nginx web app on kubernetes.

## Prerequisites and dependencies:

 - A host (Linux/Windows) with pre-installed Jenkins, Docker and any CD application (in our case Ubuntu EC2 instance + ArgoCD) 
 - A clean kubernetes cluster + deployed ingress controller and metrics-server (minikube does the job just fine) and Helm
 - A custom-built docker image with docker and git to server as and Jenkins Agent for the Jenkins pipel
##    
* All of the following steps are assuming and Ubuntu EC2 instance as a host for the CICD
* Sources:
  - [Jenkins](https://www.jenkins.io/doc/book/installing/linux/)
  - [Docker](https://docs.docker.com/desktop/install/ubuntu/#install-docker-desktop)
  - [ArgoCD](https://operatorhub.io/operator/argocd-operator)

```bash

# install java and verify installation

sudo apt update
sudo apt install openjdk-11-jre
java -version

# install Jenkins

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

# Acquire the admin pass for Jenkins
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

# Install the "Docker Pipeline" Jenkins plugin

# Install Docker

sudo apt update
sudo apt install docker.io

# Grant Jenkins user and Ubuntu user permission to Docker.

sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker

# Deploy The Argo CD Operator on the kubernetes cluster

curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.25.0/install.sh | bash -s v0.25.0
kubectl create -f https://operatorhub.io/install/argocd-operator.yaml

# After install, watch your operator come up using next command.

kubectl get csv -n operators

```
