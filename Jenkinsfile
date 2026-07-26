pipeline {

    agent any

    environment {

        IMAGE_NAME = "shivayadavjala

/sample-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "sample-app"

    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {

                sh """
                docker build \
                -t ${IMAGE_NAME}:${IMAGE_TAG} \
                -t ${IMAGE_NAME}:latest .
                """

            }
        }

        stage('Run Tests') {
            steps {

                sh '''
                pip install -r requirements.txt
                pytest
                '''

            }
        }

        stage('Docker Login') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo "$DOCKER_PASS" | docker login \
                    -u "$DOCKER_USER" \
                    --password-stdin
                    '''

                }

            }
        }

        stage('Push Docker Image') {
            steps {

                sh """
                docker push ${IMAGE_NAME}:${IMAGE_TAG}
                docker push ${IMAGE_NAME}:latest
                """

            }
        }

        stage('Deploy') {
            steps {

                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true

                docker run -d \
                --name ${CONTAINER_NAME} \
                -p 5000:5000 \
                ${IMAGE_NAME}:${IMAGE_TAG}
                """

            }
        }

    }

    post {

        success {
            echo "Application Successfully Deployed"
        }

        failure {
            echo "Pipeline Failed"
        }

        always {

            sh "docker logout || true"

        }

    }

}
