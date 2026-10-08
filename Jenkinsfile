pipeline {

    agent any

    environment {
        IMAGE_NAME = 'jenkins-prod-lab'
        IMAGE_TAG  = 'v1'
        APP_PORT   = '8080'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Building ${IMAGE_NAME}:${IMAGE_TAG}"
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
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f ${IMAGE_NAME} || true

                    docker run -d \
                        --name ${IMAGE_NAME} \
                        -p ${APP_PORT}:80 \
                        ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }
}
