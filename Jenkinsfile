pipeline {
    agent any

    tools {nodejs "nodejs"}

    environment {
        PORT = '3000'
        JWT_SECRET = 'demojenkins'
        RAILWAY_SERVICE_NAME = 'api-gateway'

    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'jenkins-demo', url: 'https://github.com/sloweyyy/jenkins-demo.git']])
                }
            }
        }

        stage('Cd into api-gateway') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'cd api-gateway'
                    } else {
                        bat 'cd api-gateway'
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'cd api-gateway'
                        sh 'npm install'
                    } else {
                        // Use batch command for Windows systems
                        bat 'npm install'
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

        stage('Run Tests') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'npm test'
                    } else {
                        bat 'npm test'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker build -t api-gateway .'
                    } else {
                        bat 'docker build -t api-gateway .'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker tag congchungonline-be ${env.GITHUB_ENV.DOCKER_USERNAME}/api-gateway'
                        sh 'docker push ${env.GITHUB_ENV.DOCKER_USERNAME}/api-gateway'
                    } else {
                        bat 'docker tag api-gateway %DOCKER_USERNAME%/api-gateway'
                        bat 'docker push %DOCKER_USERNAME%/api-gateway'
                    }
                }
            }
        }

        stage('Install Railway') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'npm install -g @railway/cli'
                    } else {
                        bat 'npm install -g @railway/cli'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'railway up --service ${RAILWAY_SERVICE_NAME}'
                    } else {
                        bat 'railway up --service ${RAILWAY_SERVICE_NAME}'
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