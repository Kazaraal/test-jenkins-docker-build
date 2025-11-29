pipeline {
    agent any

    environment {
        // The credentials ID we set in Jenkins for Docker Hub
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
        // Docker Hub registry (change to your Docker Hub username)
        DOCKER_REGISTRY = 'ken0'
        // Image name
        IMAGE_NAME = 'python-hello-world'
        // Tag for the image (using the build number)
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    dockerImage = docker.build("${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}", ".")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    docker.withRegistry('', "${DOCKER_CREDENTIALS_ID}") {
                        dockerImage.push()
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up built images to save disk space
            script {
                if (dockerImage) {
                    dockerImage.forceRemove()
                }
            }
        }
    }
}