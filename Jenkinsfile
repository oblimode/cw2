pipeline {
    agent any
    environment {
        DOCKERID = 'oblimode'
	DOCKERHUB_CREDENTIALS= credentials('jenkins')
	SSH_CREDENTIALS_ID = 'my-ssh-key'
        SSH_HOST = 'ec2-3-80-111-125.compute-1.amazonaws.com'
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
	 stage('SSH and Run Commands') {
            steps {
                sshagent(credentials: [SSH_CREDENTIALS_ID]) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@$SSH_HOST << EOF
                        cd ansibleplaybooks
                        ansible-playbook rollingupdate-scale-pb.yml
                        exit
			EOF
                    '''
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
