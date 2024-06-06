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
                    // Add Jenkins user to docker group
                    sh 'sudo usermod -aG docker jenkins'

                    // Install Docker if not already installed
                    sh '''
                        if ! [ -x "$(command -v docker)" ]; then
                          curl -fsSL https://get.docker.com -o get-docker.sh
                          sh get-docker.sh
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
                        docker container prune -f
                        docker image prune -f
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image using the Dockerfile in the 'app' directory
                script {
                    dir('app') {
                        try {
                            dockerImage = docker.build("${env.DOCKER_IMAGE}:${env.DOCKER_TAG}")
                        } catch (Exception e) {
                            echo "Error building Docker image: ${e.getMessage()}"
                            currentBuild.result = 'FAILURE'
                            error("Docker build failed")
                        }
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                // Run the Docker container
                script {
                    dockerImage.run('-d -p 5500:5500 --name flask-app')
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace and stop/remove the container after the build
            script {
                try {
                    sh 'docker stop flask-app'
                    sh 'docker rm flask-app'
                } catch (Exception e) {
                    echo 'No container to clean up.'
                }
                cleanWs()
            }
        }
    }
}