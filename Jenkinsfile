
pipeline{

	agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('docker-premasaik')
		POD_NAME = "tmp"
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
					sh 'scp -v -r -o StrictHostKeyChecking=no /home/premasai/CICD2/deployment.yaml premasai@127.0.0.1:/home/premasai'
					
					script{
						try{
							sh 'ssh premasai@127.0.0.1 kubectl apply -f deployment.yaml'
							sh 'ssh premasai@127.0.0.1 kubectl get pods | grep kubernetes-101-*'
							ret = sh ( script:'ssh premasai@127.0.0.1 kubectl get pods | grep kubernetes-101-* | awk \'{print $1}\'',returnStdout: true).trim()
							println ret	
							"echo came here : ${ret}"
							ret2 = sh ( script:"ssh premasai@127.0.0.1 kubectl port-forward  ${ret} 3001:3000",returnStdout: true).trim()
							sh 'ssh premasai@127.0.0.1 echo ret'
							}catch(error)
							{
							 sh 'ssh premasai@127.0.0.1 kubectl create -f deployment.yaml'
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
