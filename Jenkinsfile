pipeline {
    agent any
    def dockerapp

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // CORREÇÃO AQUI: O CONTEXTO DEVE SER UM DIRETÓRIO.
                    // Se o Dockerfile está em ./src/Dockerfile, o contexto é ./src/
                    // Ou, se o Dockerfile está na raiz do repositório, o contexto é '.' (ponto)
                    dockerapp = docker.build("leandro/guia-jenkins1:${env.BUILD_ID}", "./src") // Contexto é o diretório 'src'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
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