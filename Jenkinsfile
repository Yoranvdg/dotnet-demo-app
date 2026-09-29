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
                    -e MYSQL_DATABASE=TodoDb \
                    mariadb:latest
                '''
            }
        }
        stage('Wait for Database') {
            steps {
                // Vraagt actief aan MariaDB of hij al klaar is door een simpele query uit te voeren
                sh '''
                    echo "Waiting for MariaDB to be fully ready..."
                    until docker exec dbrunning mysql -uroot -psecret -e "SELECT 1;" >/dev/null 2>&1; do
                        echo "Database is still booting, waiting 3 seconds..."
                        sleep 3
                    done
                    echo "Database is online and ready!"
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
                    -e ConnectionStrings__DefaultConnection="Server=dbrunning;Database=TodoDb;User=root;Password=secret;" \
                    dotnet-demo-app
                '''
            }
        }
    }
}