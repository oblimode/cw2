pipeline {
    agent any
    environment {
        DOCKERID = 'oblimode'
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
                    sh 'docker run -d --rm --name test-cw2 -p 8080:8080 $DOCKERID/cw2-server:1.0'
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
            }
        }
    }
}
