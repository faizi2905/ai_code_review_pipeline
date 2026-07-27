pipeline{
    agent any

    environment {
        APP_NAME = "ai-review-app"
        IMAGE_TAG = "${BUILD_NUMBER}" 
        CONTAINER_NAME = "ai-review-container"
        PORT = "8080"
    }

    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }

    stages {
        stage('Checkout Source') {
            steps {
                checkout scm
                }
            }

        stage('Check Docker') {
            steps {
                bat 'where docker'
                bat '"C:\\Users\\chikk\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe" --version'
            }
        }
            
            
        stage('Build') {
            steps {
                bat 'mvn clean compile'
                }
            }
            
        stage('Run Unit Tests') {
            steps {
                bat 'mvn test -Dtest=!PostgresIntegrationTests'
                }
            }
            
        stage('Package Application') {
            steps {
                bat 'mvn package -DskipTests'
                }
            }
            
        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${APP_NAME}:${IMAGE_TAG} -t ${APP_NAME}:latest ."
                }
            }
            
        stage('Run Docker Container') {
            steps {
                script{
                    bat "docker stop ${CONTAINER_NAME} || true"
                    bat "docker rm ${CONTAINER_NAME} || true"

                    bat "docker run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME} ${APP_NAME}:latest"
                }
                }
            }
            
        stage('Health Check') {
            steps {
                echo "Waiting for application to start ....."
                bat """
                    sleep 10
                    curl -fail http://localhost:${PORT}/actuator/health || curl -fail http://localhost:${PORT}/
                """
            }
    }
    }

}
