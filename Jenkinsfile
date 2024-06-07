pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github_pat')
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    git url: 'https://github.com/jross-mm/DevOps-Tooling.git',
                        branch: 'main',
                        credentialsId: 'github_pat'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    writeFile file: 'Dockerfile', text: '''
                    FROM nginx:alpine
                    COPY nginx.conf /etc/nginx/nginx.conf
                    '''

                    writeFile file: 'nginx.conf', text: '''
                    events {}
                    http {
                        server {
                            listen 80;
                            location / {
                                return 200 'Hello Jenkins!';
                                add_header Content-Type text/plain;
                            }
                        }
                    }
                    '''
                }
                sh 'docker build -t my-nginx .'
                sh 'echo $SECRET_VAR'
            }
        }
    }
}
