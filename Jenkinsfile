pipeline {

    agent any

    environment {
        IMAGE_NAME = 'jenkins-prod-lab'
        IMAGE_TAG  = 'v1'
        APP_PORT   = '808'
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

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} \
                            ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}

                        docker push \
                            ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}

                        docker logout
                    '''
                }
            }
        }
        stage('Show Environment') {
    steps {
        echo "Selected environment: ${params.DEPLOY_ENV}"
    }
}

        stage('Deploy') {
    steps {
        echo "Deploying to ${params.DEPLOY_ENV}"

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
