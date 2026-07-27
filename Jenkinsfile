pipeline{
    agent any

    environment {
        APP_NAME = "ai-review-app"
        IMAGE_TAG = "${BUILD_NUMBER}" 
        CONTAINER_NAME = "ai-review-container"
        PORT = "8080"
    }

    tools {
        maven 'mvn'
        jdk 'jdk'
    }

    stages {
        stage('Checkout Source') {
            steps {
                checkout scm
                }
            }
            
            
        stage('Build') {
            steps {
                sh 'mvn clean compile'
                }
            }
            
        stage('Run Unit Tests') {
            steps {
                sh 'mvn test'
                }
            }
            
        stage('Package Application') {
            steps {
                sh 'mvn package -DskipTests'
                }
            }
            
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${APP_NAME}:${IMAGE_TAG} -t ${APP_NAME}:latest ."
                }
            }
            
        stage('Run Docker Container') {
            steps {
                script{
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"

                    sh "docker run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME} ${APP_NAME}:latest"
                }
                }
            }
            
        stage('Health Check') {
            steps {
                echo "Waiting for application to start ....."
                sh """
                    sleep 10
                    curl -fail http://localhost:${PORT}/actuator/health || curl -fail http://localhost:${PORT}/
                """
            }
    }
    }

}
