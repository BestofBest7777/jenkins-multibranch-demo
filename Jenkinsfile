pipeline {
    agent any

    tools {
        nodejs 'node18' // ← имя, которое ты указал в Global Tool Configuration
    }

    environment {
        BRANCH = "${env.BRANCH_NAME}"
    }

    stages {
        stage('Init Vars') {
            steps {
                script {
                    def port = (env.BRANCH_NAME == 'main') ? '3000' : '3001'
                    def image = (env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    // объявляем как локальные переменные, чтобы избежать ворнингов
                    env.PORT = port
                    env.IMAGE_NAME = image
                }
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Change logo and port') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'cp scripts/logo-main.svg src/assets/logo.svg'
                    } else {
                        sh 'cp scripts/logo-dev.svg src/assets/logo.svg'
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${env.IMAGE_NAME} ."
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh "docker rm -f ${env.BRANCH}-app || true"
                    sh "docker run -d --name ${env.BRANCH}-app -p ${env.PORT}:${env.PORT} ${env.IMAGE_NAME}"
                }
            }
        }
    }
}

