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

        stage('Stop Old Container') {
            steps {
                sh 'docker stop employee-app || true'
                sh 'docker rm employee-app || true'
            }
        }

        stage('Run Application') {
            steps {
                sh 'docker run -d --name employee-app -p 8082:8082 employee-management-backend:latest'
            }
        }
    }
}
