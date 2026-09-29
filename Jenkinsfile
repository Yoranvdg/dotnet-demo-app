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
                sh 'docker build -t dotnet-demo-app ./TodoApp'
            }
        }
        stage('Run Container') {
            steps {
                // Start de net gebouwde .NET app container op poort 8080
                sh 'docker run -d --name dotnetrunning -p 8080:8080 dotnet-demo-app'
            }
        }
    }
}