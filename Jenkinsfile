
pipeline{

	agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('docker-premasaik')
	}

	stages {

		stage('Build') {

			steps {
				sh 'docker build -t premasaik/kubernetes-101:v2 .'
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {

			steps {
				sh 'docker push premasaik/kubernetes-101:v2'
			}
		}
		
		stage('Deploy to K8s')
		{
			steps{
				sshagent(['premasaik-k8s'])
				{
					sh 'scp -r -o StrictHostKeyChecking=no deployment.yaml premasaik@127.0.0.1:/home/premasai/CICD2/'
					
					script{
						try{
							sh 'ssh premasaik@127.0.0.1 kubectl apply -f /home/premasai/CICD2/deployment.yaml'

							}catch(error)
							{
							 sh 'ssh premasaik@127.0.0.1 kubectl create -f /home/premasai/CICD2/deployment.yaml'
							}
					}
				}
			}
		}
	}

	post {
		always {
			sh 'docker logout '
		}
	}

}
