pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                catchError(buildResult: 'SUCCESS') {
                    sh 'docker compose down'
                }
            }
        }
        stage('Unit Tests') {
            steps {
                sh 'dotnet test'
            }
        }
        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker compose up -d --build'
            }
        }
    }
}