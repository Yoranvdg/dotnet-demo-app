pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                catchError(buildResult: 'SUCCESS') {
                    // Stopt eventueel oude losse containers of compose stacks
                    sh 'docker stop dotnetrunning || true'
                    sh 'docker rm dotnetrunning || true'
                    sh 'docker compose down || true'
                }
            }
        }
        stage('Deploy with Docker Compose') {
            steps {
                // Start de complete stack (app + database) op basis van de docker-compose.yml
                sh 'docker compose up -d --build'
            }
        }
    }
}