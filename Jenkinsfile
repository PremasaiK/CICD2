
pipeline{

	agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('docker-premasaik')
		POD_NAME = "tmp"
	}

	stages {

		stage('Build') {

			steps {
				sh 'docker build -t premasaik/kubernetes-101:v3 .'
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {

			steps {
				sh 'docker push premasaik/kubernetes-101:v3'
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
							sleep 5
							ret1 = sh ( script:'ssh premasai@127.0.0.1 kubectl get pods | grep kubernetes-101-* | awk \'{print $3}\'',returnStdout: true).trim()
							println ret1
							ret2 = sh ( script:'ssh premasai@127.0.0.1 kubectl get pods | grep kubernetes-101-* | awk \'{print $1}\'',returnStdout: true).trim()
							println "${ret2}"	
							echo "came here : ${ret2}"
							env.POD_NAME = "${ret2}"
							echo "env : ${env.POD_NAME}"
						        if (ret1 == "Running") {
								ret3 = sh ( script:"ssh premasai@127.0.0.1 kubectl port-forward  ${ret2} :3000 & ",returnStdout: true).trim()
								sleep 5
								println "${ret3}"
								echo " ${ret3} | awk -F'[: ]' 'NR==1{print \$4}' "
	     							}
							else {
								println "${ret1}"
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
