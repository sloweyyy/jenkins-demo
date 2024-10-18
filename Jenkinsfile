pipeline {
    agent any

    tools {
        nodejs 'nodejs'
    }

    environment {
        PORT = '3000'
        JWT_SECRET = 'demojenkins'
        RAILWAY_SERVICE_NAME = 'jenkins-demo'
        MONGODB_URL = "${MONGODB_URL}"
        DOCKER_IMAGE_NAME = 'slowey/jenkins-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'jenkins-demo', url: 'https://github.com/sloweyyy/jenkins-demo.git']])
                }
            }
        }

        stage('Print docker password') {
            steps {
                script {
                    echo "Docker password: ${env.DOCKER_PASSWORD}"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'npm install'
                    } else {
                        bat 'npm install'
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    if (isUnix()) {
                        sh "export PORT=${PORT} && export JWT_SECRET=${JWT_SECRET} && export MONGODB_URL=${MONGODB_URL} && npm test"
                    } else {
                        bat "export PORT=${PORT} && export JWT_SECRET=${JWT_SECRET} && export MONGODB_URL=${MONGODB_URL} && npm test"
                    }
                }
            }
        }

        stage('Check Docker Installation') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker --version'
                    } else {
                        bat 'docker --version'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh "docker build -t ${DOCKER_IMAGE_NAME} ."
                    } else {
                        bat "docker build -t ${DOCKER_IMAGE_NAME} ."
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh "docker login -u slowey -p ${env.DOCKER_PASSWORD}"
                        sh "docker push ${DOCKER_IMAGE_NAME}"
                    } else {
                        bat "docker login -u slowey -p %DOCKER_PASSWORD%"
                        bat "docker push ${DOCKER_IMAGE_NAME}"
                    }
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    if (isUnix()) {
                        sh "export PORT=${PORT} && export JWT_SECRET=${JWT_SECRET} && npm start"
                    } else {
                        bat "set PORT=${PORT} && set JWT_SECRET=${JWT_SECRET} && npm start"
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                cleanWs()
            }
        }
    }
}
