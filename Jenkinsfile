pipeline {
    agent any
 
    environment {
        IMAGE_NAME = "web"
        CONTAINER_NAME = "web-container"
        HOST_PORT = "8081"
        CONTAINER_PORT = "80"
    }
 
    stages {
 
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }
 
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
 
                sh '''
                    docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                    docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
                '''
            }
        }
 
        stage('Stop Old Container') {
            steps {
                echo 'Stopping old container...'
 
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                '''
            }
        }
 
        stage('Deploy Container') {
            steps {
                echo 'Starting new container...'
 
                sh '''
                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p ${HOST_PORT}:${CONTAINER_PORT} \
                    ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }
 
        stage('Test Application') {
            steps {
                echo 'Testing web application...'
 
                sh '''
                    sleep 5
                    curl -f http://localhost:${HOST_PORT}
                '''
            }
        }
    }
 
    post {
        success {
            echo '======================================'
            echo 'Deployment Successful!'
            echo '======================================'
            echo "Application: http://SERVER-IP:${HOST_PORT}"
        }
 
        failure {
            echo '======================================'
            echo 'Deployment Failed!'
            echo '======================================'
 
            sh '''
                docker logs ${CONTAINER_NAME} || true
            '''
        }
 
        always {
            echo 'Jenkins pipeline completed.'
        }
    }
}
