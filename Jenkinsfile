pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                sh "echo \"Executando o comando Docker Build\"" // Aspas duplas escapadas
            }
        }
        stage('Push Docker Image') {
            steps {
                sh "echo \"Executando o comando Docker push\"" // Aspas duplas escapadas
            }
        }
        stage('Deploy no Kubernetes') {
            steps {
                sh "echo \"Executando o comando kubectl apply\"" // Aspas duplas escapadas
            }
        }
    }
}