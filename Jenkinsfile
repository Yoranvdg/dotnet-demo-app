pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                catchError(buildResult: 'SUCCESS') {
                    sh 'docker-compose down'
                }
            }
        }
        stage('Build and Test via Docker') {
            steps {
                // In plaats van 'dotnet test' lokaal te draaien, 
                // laten we Docker Compose de hele app en tests bouwen en opstarten
                sh 'docker-compose up -d --build'
            }
        }
    }
}