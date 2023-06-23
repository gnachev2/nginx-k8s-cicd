pipeline {
  agent {
    docker { image 'gnachev/jenkins-agent:v1'
    args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
   }
  }
  stages {
    stage('Checkout') {
      steps {
        sh 'echo passed'
        //git branch: 'main', url: 'https://github.com/gnachev2/nginx-k8s-cicd.git'
      }
    }
    stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "gnachev/nginx-sap-test-project:${BUILD_NUMBER}"
        // DOCKERFILE_LOCATION = "nginx-docker-build/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-credentials')
      }
      steps {
        script {
            sh 'cd nginx-docker-build/ && docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-credentials") {
                dockerImage.push()
            }
         }
      }
    }
    stage('Update Deployment File') {
      environment {
        GIT_REPO_NAME = "nginx-k8s-cicd"
        GIT_USER_NAME = "gnachev2"
      }
      steps {
        withCredentials([string(credentialsId: 'github-credentials', variable: 'GITHUB_TOKEN')]) {
            sh '''
		#!/bin/bash
                git config user.email "na4ev.bg@gmail.com"
                git config user.name "Georgi Nachev"
                BUILD_NUMBER=${BUILD_NUMBER}
		LATEST_TAG=$(grep "image:" nginx-k8s-manifests/deployment.yaml | cut -d":" -f3)
		sed -i "s/:${LATEST_TAG}/:${BUILD_NUMBER}/g" nginx-k8s-manifests/deployment.yaml
		git add nginx-k8s-manifests/deployment.yaml
		git commit -am "Update deployment image from version ${LATEST_TAG} to version ${BUILD_NUMBER}"
		git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
            '''
        }
      }
    }
  }
}
