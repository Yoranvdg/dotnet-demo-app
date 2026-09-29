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
                // Map naar poort 8081 zodat deze niet botst met Jenkins op poort 8080
                sh 'docker run -d --name dotnetrunning -p 8081:8080 dotnet-demo-app'
            }
        }
    }
}