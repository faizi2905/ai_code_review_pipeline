pipeline{
    agent any

    environment {
        APP_NAME = "ai-review-app"
        IMAGE_TAG = "${BUILD_NUMBER}" 
        CONTAINER_NAME = "ai-review-container"
        PORT = "8080"
        DOCKER = "C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe"
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
                bat "\"${DOCKER}\"  build -t ${APP_NAME}:${IMAGE_TAG} -t ${APP_NAME}:latest ."
                }
            }
            
        stage('Run Docker Container') {
            steps {
                script{
                    bat "\"${DOCKER}\"  stop ${CONTAINER_NAME} || exit /b 0"
                    bat "\"${DOCKER}\"  rm ${CONTAINER_NAME} || exit /b 0"

                    bat "\"${DOCKER}\"  run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME} ${APP_NAME}:latest"
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
