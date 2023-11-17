pipeline {
    agent any

    environment {
        NODEJS_HOME = tool 'NodeJS' 
        MAIN_DOCKER_IMAGE = 'nodemain:v1.0'
        DEV_DOCKER_IMAGE = 'nodedev:v1.0'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                script {
                    // Install dependencies
                    sh 'npm install'

                    // Run tests
                    sh 'npm test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Stop and remove existing containers
                    sh 'docker stop main-container || true'
                    sh 'docker rm main-container || true'
                    sh 'docker stop dev-container || true'
                    sh 'docker rm dev-container || true'

                    // Build Docker image for both main and dev branches
                    sh 'docker build -t $MAIN_DOCKER_IMAGE .'
                    sh 'docker build -t $DEV_DOCKER_IMAGE .'
                }
            }
        }

        stage('Run Docker Container') {
            when {
                anyOf {
                    branch 'main'
                    branch 'dev'
                }
            }
            steps {
                script {
                    // Run Docker container based on the branch
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker run -d --expose 3000 -p 3000:3000 --name main-container $MAIN_DOCKER_IMAGE'
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker run -d --expose 3001 -p 3001:3000 --name dev-container $DEV_DOCKER_IMAGE'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
    }
}