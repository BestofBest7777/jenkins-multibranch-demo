pipeline {
    agent any

    environment {
        BRANCH = "${env.BRANCH_NAME}"
        PORT = (env.BRANCH_NAME == 'main') ? '3000' : '3001'
        IMAGE_NAME = (env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0'
    }

    stages {
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
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh "docker rm -f ${BRANCH}-app || true"
                    sh "docker run -d --name ${BRANCH}-app -p ${PORT}:${PORT} ${IMAGE_NAME}"
                }
            }
        }
    }
}

