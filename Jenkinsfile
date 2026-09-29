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
        stage('Build with Dockerfile in TodoApp') {
            steps {
                // Als de Dockerfile in de TodoApp map staat, bouwen we vanaf daar
                sh 'docker build -t dotnet-demo-app ./TodoApp'
            }
        }
    }
}