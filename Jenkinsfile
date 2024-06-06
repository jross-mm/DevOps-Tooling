pipeline {
    agent any

    environment {
        // Define environment variables
        DOCKER_IMAGE = "my-flask-app"
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Install Docker if not already installed
                    sh '''
                        if ! [ -x "$(command -v docker)" ]; then
                          curl -fsSL https://get.docker.com -o get-docker.sh
                          sudo sh get-docker.sh
                          rm get-docker.sh
                        fi
                    '''
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    // Remove any existing Docker containers and images to free up space
                    sh '''
                        docker container prune -f || true
                        docker image prune -f || true
                    '''
                }
            }
        }

        stage('Checkout') {
            steps {
                // Checkout the code from the repository
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image using the Dockerfile in the 'app' directory
                script {
                    dir('app') {
                        sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                // Run the Docker container
                script {
                    sh 'docker run -d -p 5500:5500 --name flask-app ${DOCKER_IMAGE}:${DOCKER_TAG}'
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace and stop/remove the container after the build
            script {
                sh 'docker stop flask-app || true'
                sh 'docker rm flask-app || true'
                cleanWs()
            }
        }
    }
}
