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
                    def dbContainer = docker.image("${DOCKERHUB_REPO}:mysql-db").run('-d -e MYSQL_ROOT_PASSWORD=rootpassword -e MYSQL_DATABASE=flask-db --name mysql-db')
                    def appContainer = docker.image("${DOCKERHUB_REPO}:flask-app").run("-d --link mysql-db:mysql -p 5000:5000 --name flask-app")
                }
            }
        }
    }

    //post {
    //    always {
    //        script {
    //            //sh 'docker rm -f flask-app || true'
    //            //sh 'docker rm -f mysql-db || true'
    //        }
    //    }
    //}
}