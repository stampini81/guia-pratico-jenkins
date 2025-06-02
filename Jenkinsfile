pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script { // Necessário para usar 'docker.' em 'steps'
                    // O Docker Pipeline já sabe qual imagem está construindo
                    // e a disponibiliza para a etapa 'docker.image(...)'
                    docker.build("leandro282/guia-jenkins1:${env.BUILD_ID}", "./src")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script { // Necessário para usar 'docker.' em 'steps'
                    // Usa a imagem construída no estágio anterior pelo seu nome/tag
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        docker.image("leandro/guia-jenkins1:${env.BUILD_ID}").push('latest')
                        docker.image("leandro/guia-jenkins1:${env.BUILD_ID}").push()
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