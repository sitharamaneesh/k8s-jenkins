pipeline {
  environment {
    dockerimagename = "sitharamaneesh/react-app"
    dockerImage = ""
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

#    stage('Configure Kubernetes credentials') {
#      steps {
#        withCredentials([kubeconfigFile(credentialsId: 'kubeconfig-credentials', variable: 'KUBECONFIG')]) {
#          // Set the KUBECONFIG environment variable
#          sh 'export KUBECONFIG=$KUBECONFIG'
#        }
#      }
#    }

    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          kubernetesDeploy configs: 'deployment.yaml,service.yaml', kubeconfigId: 'kubeconfig-credentials'
        }
      }
    }
  }
}

