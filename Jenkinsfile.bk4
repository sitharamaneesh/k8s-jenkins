pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
    }

    stages {
        stage('Build') {
            steps {
                script {
                    docker.image('docker:stable').inside {
                        sh 'docker build -t sitharamaneesh/react-app .'
                    }
                }
            }
        }

        stage('Login') {
            steps {
                script {
                    docker.image('docker:stable').inside {
                        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                    }
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.image('docker:stable').inside {
                        sh 'docker push sitharamaneesh/react-app'
                    }
                }
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
            script {
                docker.image('docker:stable').inside {
                    sh 'docker logout'
                }
            }
        }
    }
}

