pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'ls -la'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'test -f index.html'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t jenkins-prod-lab:v1 .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f jenkins-prod-lab || true
                    docker run -d \
                        --name jenkins-prod-lab \
                        -p 8080:80 \
                        jenkins-prod-lab:v1
                '''
            }
        }
    }
}
