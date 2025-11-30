pipeline {
    agent {
        docker {
            image 'jenkins-docker-ken:1.0'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    
    environment {
        DOCKER_IMAGE = 'ken0/python-hello-world'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', 
                    url: 'https://github.com/Kazaraal/test-jenkins-docker-build.git',
                    credentialsId: '' // Only needed for private repos
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }
        
        stage('Test Docker Image') {
            steps {
                script {
                    // Run the container and capture output
                    def output = sh(
                        script: "docker run --rm ${DOCKER_IMAGE}:${DOCKER_TAG}",
                        returnStdout: true
                    ).trim()
                    
                    // Verify the output contains expected text
                    if (!output.contains('Hello, World from Docker!')) {
                        error 'Docker container test failed!'
                    }
                    
                    echo "Test passed! Output: ${output}"
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', 'docker-hub-credentials') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
        
        stage('Cleanup') {
            steps {
                script {
                    // Remove the local image to save space
                    sh "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG} || true"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed!'
        }
        success {
            echo "Successfully built and pushed ${DOCKER_IMAGE}:${DOCKER_TAG}"
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}