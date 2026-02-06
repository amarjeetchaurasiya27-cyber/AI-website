pipeline {
    agent any

    environment {
        IMAGE_NAME = "amarjeet001/ai-website"
        DOCKER_TAG = "01"
        CONTAINER_NAME = "ai-website-container"
        HOST_PORT = "3490"
        CONTAINER_PORT = "80"
        DOCKER_CREDENTIALS = "dockerhub-creds"
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/amarjeetchaurasiya27-cyber/AI-website.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%DOCKER_TAG% ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDENTIALS}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"
                }
            }
        }

        stage('Push Image') {
            steps {
                bat "docker push %IMAGE_NAME%:%DOCKER_TAG%"
            }
        }

        stage('Deploy Container') {
            steps {
                bat """
                docker rm -f %CONTAINER_NAME% 2>nul
                docker run -d ^
                --name %CONTAINER_NAME% ^
                -p %HOST_PORT%:%CONTAINER_PORT% ^
                --restart unless-stopped ^
                %IMAGE_NAME%:%DOCKER_TAG%
                """
            }
        }
    }
}
