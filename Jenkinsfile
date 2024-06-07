pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id')
        DOCKERHUB_REPO = 'jonny2402/devops-tooling'
    }

    stages {
        stage('Build Flask App Image') {
            steps {
                script {
                    docker.build("${DOCKERHUB_REPO}:flask-app", './flask-app')
                }
            }
        }

        stage('Build MySQL Image') {
            steps {
                script {
                    docker.build("${DOCKERHUB_REPO}:mysql-db", './db')
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

        stage('Deploy') {
            steps {
                script {
                    // Start MySQL container
                    docker.image("${DOCKERHUB_REPO}:mysql-db").withRun('-e MYSQL_ROOT_PASSWORD=rootpassword -e MYSQL_DATABASE=flask-db --name mysql-db') { db ->
                        // Start Flask app container
                        docker.image("${DOCKERHUB_REPO}:flask-app").withRun("--link mysql-db:mysql -p 5000:5000 --name flask-app") { app ->
                            // Run tests or other deployment steps here
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                // Clean up containers after the build
                sh 'docker rm -f flask-app || true'
                sh 'docker rm -f mysql-db || true'
            }
        }
    }
}