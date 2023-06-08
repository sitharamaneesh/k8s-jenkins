pipeline{

	agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub-credentials')
	}

	
stages {

    stage('Initialize')
    {
        def dockerHome = tool 'MyDocker'
        def mavenHome  = tool 'MyMaven'
        env.PATH = "${dockerHome}/bin:${mavenHome}/bin:${env.PATH}"
    }

    stage('Checkout') 
    {
        checkout scm
    }

      stage('Build') 
           {
            sh 'uname -a'
            sh 'mvn -B -DskipTests clean package'  
          }

        stage('Test') 
        {
            //sh 'mvn test'
            sh 'ifconfig' 
        }

        stage('Deliver') 
          {
                sh 'bash ./jenkins/deliver.sh'
        }


	

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


		stage('Deploy to K8s')
		{
			steps{
				sshagent(['k8s-jenkins'])
				{
					sh 'scp -r -o StrictHostKeyChecking=no node-deployment.yaml root@34.138.19.121:/root'
					
					script{
						try{
							sh 'ssh root@34.138.19.121 kubectl apply -f /root/deployment.yaml --kubeconfig=/root/kube.yaml'

							}catch(error)
							{

							}
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
