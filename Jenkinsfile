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
                    -e MARIADB_ROOT_PASSWORD=sekrit \
                    -e MARIADB_DATABASE=todo_db \
                    -e MARIADB_USER=todo_usr \
                    -e MARIADB_PASSWORD=letmeinplz \
                    mariadb:11
                '''
            }
        }
        stage('Wait for Database & Init Schema') {
            steps {
                sh '''
                    echo "Waiting for MariaDB to accept connections..."
                    for i in {1..30}; do
                        if docker exec dbrunning mariadb -uroot -psekrit -e "SELECT 1;" >/dev/null 2>&1; then
                            echo "MariaDB is up and running!"
                            break
                        fi
                        echo "Attempt $i: MariaDB is starting up, waiting 3 seconds..."
                        sleep 3
                    done
                    
                    echo "Creating todos table..."
                    docker exec -i dbrunning mariadb -utodo_usr -pletmeinplz todo_db << 'EOF'
                    CREATE TABLE IF NOT EXISTS todos (
                        id INT AUTO_INCREMENT PRIMARY KEY,
                        title VARCHAR(255) NOT NULL,
                        is_done BOOLEAN NOT NULL DEFAULT FALSE,
                        created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP
                    );
                    EOF
                    echo "Database schema initialized successfully!"
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
                    -e ConnectionStrings__DefaultConnection="Server=dbrunning;Port=3306;Database=todo_db;Uid=todo_usr;Pwd=letmeinplz;" \
                    dotnet-demo-app
                '''
            }
        }
    }
}