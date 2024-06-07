pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id')
        DOCKERHUB_REPO = 'jonny2402/devops-tooling'
        DOCKER_NETWORK = 'my-network'
    }

    stages {
        stage('Clean Up Existing Containers') {
            steps {
                script {
                    // Stop and remove existing containers if they are running
                    sh 'docker rm -f flask-app || true'
                    sh 'docker rm -f mysql-db || true'
                    sh 'docker rm -f nginx || true'
                    sh 'docker network rm ${DOCKER_NETWORK} || true'
                }
            }
        }

        stage('Create Docker Network') {
            steps {
                script {
                    sh 'docker network create ${DOCKER_NETWORK}'
                }
            }
        }

        stage('Build Flask App Image') {
            steps {
                script {
                    dir ('Task2/flask-app') {
                        docker.build("${DOCKERHUB_REPO}:flask-app")
                    }
                }
            }
        }

        stage('Build MySQL Image') {
            steps {
                script {
                    dir ('Task2/db') {
                        docker.build("${DOCKERHUB_REPO}:mysql-db")
                    } 
                }
            }
        }

        stage('Build Nginx Image') {
            steps {
                script {
                    dir('Task2/nginx') {
                        docker.build("${DOCKERHUB_REPO}:nginx")
                    }
                }
            }
        }

        stage('Push Flask App Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials-id') {
                        docker.image("${DOCKERHUB_REPO}:flask-app").push('latest')
                    }
                }
            }
        }

        stage('Push MySQL Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials-id') {
                        docker.image("${DOCKERHUB_REPO}:mysql-db").push('latest')
                    }
                }
            }
        }

        stage('Push Nginx Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials-id') {
                        docker.image("${DOCKERHUB_REPO}:nginx").push('latest')
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def dbContainer = docker.image("${DOCKERHUB_REPO}:mysql-db").run("-d --network ${DOCKER_NETWORK} -e MYSQL_ROOT_PASSWORD=rootpassword -e MYSQL_DATABASE=flask-db --name mysql-db")
                    sleep 10
                    def appContainer = docker.image("${DOCKERHUB_REPO}:flask-app").run("-d --network ${DOCKER_NETWORK} --link mysql-db:mysql -p 5000:5000 --name flask-app")
                    // Wait until Flask app is healthy with a maximum timeout of 60 seconds
                    sh '''
                    timeout=60
                    elapsed=0
                    while [ "$(docker inspect -f {{.State.Health.Status}} flask-app)" != "healthy" ]; do
                        if [ $elapsed -ge $timeout ]; then
                            echo "Timeout reached while waiting for flask-app to become healthy"
                            exit 1
                        fi
                        echo "Waiting for flask-app to become healthy..."
                        sleep 5
                        elapsed=$((elapsed + 5))
                    done
                    '''
                    def nginxContainer = docker.image("${DOCKERHUB_REPO}:nginx").run("-d --network ${DOCKER_NETWORK} --link flask-app:flask-app -p 80:80 --name nginx")
                }
            }
        }
    }

    post {
        always {
            script {
                // Clean up unused Docker resources
                sh 'docker system prune -f'
            }
        }
    }
}