pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                catchError(buildResult: 'SUCCESS') {
                    // Ruimt oude containers op als ze al bestaan
                    sh 'docker stop dotnetrunning dbrunning || true'
                    sh 'docker rm dotnetrunning dbrunning || true'
                    sh 'docker network rm net-demo || true'
                }
            }
        }
        stage('Create Network') {
            steps {
                // Maak een Docker netwerk zodat de app en database met elkaar kunnen praten
                sh 'docker network create net-demo'
            }
        }
        stage('Start Database') {
            steps {
                // Start een MariaDB database container
                sh '''
                    docker run -d --name dbrunning \
                    --network net-demo \
                    -e MYSQL_ROOT_PASSWORD=secret \
                    -e MYSQL_DATABASE=TodoDb \
                    mariadb:latest
                '''
            }
        }
        stage('Build Docker Image') {
            steps {
                // Bouwt de .NET app image vanuit de TodoApp map
                sh 'docker build -t dotnet-demo-app ./TodoApp'
            }
        }
        stage('Run App Container') {
            steps {
                // Start de .NET app op poort 8081 en koppel hem aan het netwerk
                sh '''
                    docker run -d --name dotnetrunning \
                    --network net-demo \
                    -p 8081:8080 \
                    -e ConnectionStrings__DefaultConnection="Server=dbrunning;Database=TodoDb;User=root;Password=secret;" \
                    dotnet-demo-app
                '''
            }
        }
    }
}