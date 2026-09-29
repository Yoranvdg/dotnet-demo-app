pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                catchError(buildResult: 'SUCCESS') {
                    sh 'podman compose down'
                }
            }
        }
        stage('Deploy with Podman Compose') {
            steps {
                // Bouwt en start de .NET app en MariaDB via podman compose
                sh 'podman compose up -d --build'
            }
        }
    }
}