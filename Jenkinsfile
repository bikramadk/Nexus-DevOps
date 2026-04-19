pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/bikramadk/Nexus-DevOps.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t todo-app .'
            }
        }

        stage('Stop Old Containers') {
            steps {
                sh 'docker compose down || true'
            }
        }

        stage('Run Containers') {
            steps {
                sh 'docker compose up -d'
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}