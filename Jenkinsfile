
pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t sitharamaneesh/react-app .'
            }
        }

        stage('Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push') {
            steps {
                sh 'docker push sitharamaneesh/react-app'
            }
        }

        stage('Deploy to K8s') {
            steps {
                script {
                    try {
                        sh 'kubectl apply -f deployment.yaml'
                        sh 'kubectl apply -f service.yaml'
                        
                    } catch (error) {
                        // Handle error
                    }
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}

