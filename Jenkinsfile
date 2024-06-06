pipeline {
    agent any

    environment {
        // Define environment variables
        FLASK_IMAGE = "my-flask-app"
        NGINX_IMAGE = "my-nginx"
        DOCKER_TAG = "latest"
        DOCKER_NETWORK = "my-network"
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
                    // Remove any existing Docker containers, images, and networks to free up space
                    sh '''
                        docker container prune -f || true
                        docker image prune -f || true
                        docker network rm ${DOCKER_NETWORK} || true
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

        stage('Build Flask Docker Image') {
            steps {
                // Build the Docker image using the Dockerfile in the 'app' directory
                script {
                    dir('app') {
                        sh 'docker build -t ${FLASK_IMAGE}:${DOCKER_TAG} .'
                    }
                }
            }
        }

        stage('Build NGINX Docker Image') {
            steps {
                // Build the Docker image for NGINX using the Dockerfile in the 'nginx' directory
                script {
                    sh 'docker build -t ${NGINX_IMAGE}:${DOCKER_TAG} -f Dockerfile.nginx'
                }
            }
        }

        stage('Create Docker Network') {
            steps {
                // Create a Docker network
                script {
                    sh 'docker network create ${DOCKER_NETWORK}'
                }
            }
        }

        stage('Run Docker Containers') {
            steps {
                // Run the Flask and NGINX Docker containers
                script {
                    sh '''
                        docker run -d --name flask-app --network ${DOCKER_NETWORK} ${FLASK_IMAGE}:${DOCKER_TAG}
                        docker run -d -p 80:80 --name nginx-proxy --network ${DOCKER_NETWORK} ${NGINX_IMAGE}:${DOCKER_TAG}
                    '''
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace and stop/remove the containers and network after the build
            script {
                sh '''
                    docker stop flask-app || true
                    docker rm flask-app || true
                    docker stop nginx-proxy || true
                    docker rm nginx-proxy || true
                    docker network rm ${DOCKER_NETWORK} || true
                '''
                cleanWs()
            }
        }
    }
}
