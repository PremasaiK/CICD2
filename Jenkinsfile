
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
	}

	post {
		always {
			sh 'docker logout'
		}
	}

}
