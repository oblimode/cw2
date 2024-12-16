pipeline {
    agent any
    environment {
        DOCKERID = 'oblimode'
	DOCKERHUB_CREDENTIALS= credentials('jenkins')
    }
    stages {
        stage('Build Image') {
            steps {
                script {
                    sh 'docker image build --tag $DOCKERID/cw2-server:1.0 .'
                }
            }
        }
        stage('Run Container') {
            steps {
                script {
                    sh 'docker run -d --rm --name test-cw2 $DOCKERID/cw2-server:1.0'
                }
            }
        }
        stage('Test Container') {
            steps {
                script {
                    sh 'docker exec test-cw2 echo "Hello from test-cw2!"'
                }
            }
        }
        stage('Login to Docker Hub') {      	
            steps {
		script {
	            sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'                		
	            echo 'Login Completed'
		}
            }           
        }   
        stage('Push Image') {
            steps {
                script {
                    sh 'docker image push $DOCKERID/cw2-server:1.0'
                }
            }
        }
    }
    post {
        always {
            script {
                sh 'docker stop test-cw2'
                sh 'docker logout'
            }
        }
    }
}
