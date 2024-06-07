pipeline {
    agent any

    //environment {
    //    GITHUB_TOKEN = credentials('github_pat')
    //}

    stages {
        stage('Build') {
            steps {
                script {
                    // Create a Dockerfile
                    writeFile file: 'Dockerfile', text: '''
                    FROM nginx:alpine
                    COPY nginx.conf /etc/nginx/nginx.conf
                    '''

                    // Create nginx.conf
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
                
                // Build Docker image
                sh 'docker build -t my-nginx .'
                
                // Print the secret variable
                sh 'echo $SECRET_VAR'
            }
        }
    }
}
