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
                git config user.email "na4ev.bg@gmail.com" && echo "COMPLETE-(git-config-mail)"
                git config user.name "Georgi Nachev" && echo "COMPLETE-(git-config-username)"
                BUILD_NUMBER=${BUILD_NUMBER} && echo "COMPLETE-(BUILD_NUMBER)"
		LATEST_IMAGE=$(`grep "image" nginx-k8s-manifests/deployment.yaml`) && echo "COMPLETE-(${LATEST_IMAGE})"
                LATEST_TAG=$(`cut -d':' -f3 ${LATEST_IMAGE}`) && echo "COMPLETE-(${LATEST_TAG})"
                sed -i "s/:${LATEST_TAG}/:${BUILD_NUMBER}/g" nginx-k8s-manifests/deployment.yaml && echo "COMPLETE-(sed)"
                git add nginx-k8s-manifests/deployment.yaml && echo "COMPLETE-(git-add)"
                git commit -am "Update deployment image to version ${BUILD_NUMBER}" && echo "COMPLETE-(git-comit)"
                git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main && echo "COMPLETE-(git-push)"
            '''
        }
      }
    }
  }
}