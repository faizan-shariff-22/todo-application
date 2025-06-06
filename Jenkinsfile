pipeline{
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = 'docker-hub-credentials'
        IMAGE_NAME = 'faizan2203/todo-application-image:latest'
        GIT_REPO = 'https://github.com/faizan-shariff-22/todo-application.git'
    }
    stages {
        stage('Checkout') {
            steps{
                git url: "${GIT_REPO}", branch: 'master', credentialsId: 'Github-creds'
            }
        }
        stage('Build with Maven') {
            steps{
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps{
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }
        stage('Push Docker Image') {
            steps{
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo $PASSWORD | docker login -u $USERNAME --password-stdin
                        docker push ${IMAGE_NAME}
                        docker logout
                    '''
                }
            }
        }
        stage('Deploy with Docker Compose') {
            steps{
                sh 'docker compose down || true'
                sh 'docker compose up -d'
            }
        }
        stage('Clean WOrkspace') {
            steps{
                sh 'rm -rf *'
            }
        }
        
    }
}