pipeline {
  environment {
    dockerimagename = "sitharamaneesh/react-app"
    dockerImage = ""
    kubeconfigPath = "/var/lib/jenkins/config"
  }

  agent any

  stages {
    stage('Checkout Source') {
      steps {
        git 'https://github.com/sitharamaneesh/k8s-jenkins.git'
      }
    }

    stage('Build image') {
      steps {
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
        registryCredential = 'dockerhub-credentials'
      }
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
            dockerImage.push('latest')
          }
        }
      }
    }

    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          sh "kubectl --kubeconfig=${kubeconfigPath} apply -f deployment.yaml"
          sh "kubectl --kubeconfig=${kubeconfigPath} apply -f service.yaml"
        }
      }
    }
  }
}

