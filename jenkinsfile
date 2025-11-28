pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub')
        SSH_CREDENTIALS = credentials('deploy-key')
        GITHUB_CREDENTIALS = credentials('github')
        BACKEND_IMAGE = 'gujarathiajay/backend-image:latest'
        FRONTEND_IMAGE = 'gujarathiajay/frontend-image:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/gujarathiajay/crud-dd-task-mean-app.git', credentialsId: 'github'
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                sh 'docker build -t $BACKEND_IMAGE ./backend'
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                sh 'docker build -t $FRONTEND_IMAGE ./frontend'
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $BACKEND_IMAGE'
                    sh 'docker push $FRONTEND_IMAGE'
                }
            }
        }

        stage('Deploy on VM') {
            steps {
                sshagent(credentials: ['deploy-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@<YOUR_VM_IP> '
                        cd ~/mean-app &&
                        docker-compose pull &&
                        docker-compose up -d
                    '
                    '''
                }
            }
        }
    }
}
