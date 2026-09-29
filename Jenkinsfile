pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                catchError(buildResult: 'SUCCESS') {
                    sh 'docker stop dotnetrunning dbrunning || true'
                    sh 'docker rm dotnetrunning dbrunning || true'
                    sh 'docker network rm net-demo || true'
                }
            }
        }
        stage('Create Network') {
            steps {
                sh 'docker network create net-demo'
            }
        }
        stage('Start Database') {
            steps {
                sh '''
                    docker run -d --name dbrunning \
                    --network net-demo \
                    -e MYSQL_ROOT_PASSWORD=secret \
                    -e MYSQL_ROOT_HOST='%' \
                    -e MYSQL_DATABASE=TodoDb \
                    mariadb:latest
                '''
            }
        }
        stage('Wait for Database') {
            steps {
                sh '''
                    echo "Waiting for MariaDB to fully initialize..."
                    sleep 15
                    echo "Database startup buffer complete!"
                '''
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t dotnet-demo-app ./TodoApp'
            }
        }
        stage('Run App Container') {
            steps {
                sh '''
                    docker run -d --name dotnetrunning \
                    --network net-demo \
                    -p 8081:8080 \
                    -e ASPNETCORE_ENVIRONMENT=Development \
                    -e ConnectionStrings__DefaultConnection="Server=dbrunning;Port=3306;Database=TodoDb;Uid=root;Pwd=secret;" \
                    dotnet-demo-app
                '''
            }
        }
    }
}