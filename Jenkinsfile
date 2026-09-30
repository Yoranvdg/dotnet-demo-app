node {
    stage('Preparation') {
        catchError(buildResult: 'SUCCESS') {
            sh 'docker compose down || true'
        }
    }
    stage('Checkout') {
        checkout scm
    }
    stage('Build and Deploy') {
        sh 'docker compose up -d --build'
    }
}