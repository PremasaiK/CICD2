
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
							ret1 = sh ( script:'ssh premasai@127.0.0.1 kubectl get pods | grep kubernetes-101-* | awk \'{print $3}\'',returnStdout: true).trim()
							println ret1
							ret2 = sh ( script:'ssh premasai@127.0.0.1 kubectl get pods | grep kubernetes-101-* | awk \'{print $1}\'',returnStdout: true).trim()
							println ret2	
							"echo came here : ${ret2}"
							if (ret1 == Running) {
							     ret3 = sh ( script:"ssh premasai@127.0.0.1 kubectl port-forward  ${ret2} :3000 &",returnStdout: true).trim()
							     println ret3
							     }
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
