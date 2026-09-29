pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                catchError(buildResult: 'SUCCESS') {
                    sh 'docker stop dotnetrunning || true'
                    sh 'docker rm dotnetrunning || true'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                // Bouw de Docker image van de .NET app
                // (In de TodoApp map zit de Dockerfile of we gebruiken de root)
                sh 'docker build -t dotnet-demo-app .'
            }
        }
        stage('Run Container') {
            steps {
                // Start de container op poort 8080
                sh 'docker run -d --name dotnetrunning -p 8080:8080 dotnet-demo-app'
            }
        }
    }
}