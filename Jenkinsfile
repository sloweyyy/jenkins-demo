pipeline {
    agent any

    tools {nodejs "nodejs"}

    environment {
        PORT = '3000'
        JWT_SECRET = 'demojenkins'
        RAILWAY_SERVICE_NAME = 'jenkins-demo'
        MONGODB_URL = 'mongodb+srv://22521145:slowey@mdp.9dkir.mongodb.net/'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'jenkins-demo', url: 'https://github.com/sloweyyy/jenkins-demo.git']])
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

        stage('Login to Railway') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'railway login'
                    } else {
                        bat 'railway login'
                    }
                }
            }
        }

        stage('Link Railway') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'railway link'
                    } else {
                        bat 'railway link'
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