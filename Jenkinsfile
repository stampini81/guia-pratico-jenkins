pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script { // Adicionado o bloco 'script' para usar 'dockerapp'
                    def dockerapp = docker.build("leandro/guia jenkins1:${env.BUILD_ID}", "./src/Dockerfile") // 'def dockerapp =' e '.f /src/Dockerfile'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest') // Usando 'dockerapp'
                        dockerapp.push("${env.BUILD_ID}") // Usando 'dockerapp'
                    }
                }
            }
        }
        stage('Deploy no Kubernetes') {
            steps {
                sh "echo \"Executando o comando kubectl apply\""
            }
        }
    }
}