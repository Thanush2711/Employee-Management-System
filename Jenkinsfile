pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Thanush2711/Employee-Management-System.git'
            }
        }

        stage('Maven Build and Test') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t employee-management-backend:latest .'
            }
        }

        stage('Prepare Docker Network') {
            steps {
                sh 'docker network inspect ems-network >/dev/null 2>&1 || docker network create ems-network'
                sh 'docker network connect ems-network mysqldb 2>/dev/null || true'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh 'docker stop employee-app || true'
                sh 'docker rm employee-app || true'
            }
        }

        stage('Run Application') {
            steps {
                sh '''
                    docker run -d \
                    --name employee-app \
                    --network ems-network \
                    -p 8082:8082 \
                    -e SPRING_DATASOURCE_URL="jdbc:mysql://mysqldb:3306/EMS?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true" \
                    employee-management-backend:latest
                '''
            }
        }

        stage('Verify Application') {
            steps {
                sh '''
                    for i in {1..12}; do
                        if curl -fsS http://localhost:8082/api/v1/employees; then
                            echo "Application is running successfully!"
                            exit 0
                        fi
                        echo "Waiting for application..."
                        sleep 5
                    done

                    echo "Application failed to start."
                    docker logs employee-app
                    exit 1
                '''
            }
        }
    }
}
