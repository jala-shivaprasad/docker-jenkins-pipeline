pipeline {

    agent any

    environment {
        IMAGE_NAME = "shivayadavjala/sample-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "sample-app"
    }

    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh """
                    docker build \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} \
                    -t ${IMAGE_NAME}:latest .
                """
            }
        }

        stage('Run Tests') {
            steps {
                echo "Installing dependencies and running tests..."
                sh '''
                    python3 -m pip install --upgrade pip
                    pip3 install -r requirements.txt
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

        stage('Deploy Container') {
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
            echo "===================================="
            echo "Pipeline executed successfully!"
            echo "Application deployed successfully."
            echo "===================================="
        }

        failure {
            echo "===================================="
            echo "Pipeline failed!"
            echo "Check the console output for details."
            echo "===================================="
        }

        always {
            sh 'docker logout || true'
            cleanWs()
        }
    }
}
