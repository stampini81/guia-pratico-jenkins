pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Constrói a imagem e já a taggeia com o BUILD_ID
                    docker.build("leandro282/guia-jenkins1:${env.BUILD_ID}", "./src")
                    // Define uma variável de ambiente para o nome base da imagem, útil para o push
                    env.DOCKER_IMAGE_NAME = "leandro282/guia-jenkins1"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Assegura que você está autenticado no Docker Hub usando as credenciais 'dockerhub'
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {

                        // 1. Faz o push da imagem com a tag BUILD_ID
                        // O comando sh 'docker push' automaticamente usa o login feito pelo withRegistry
                        sh "docker push ${env.DOCKER_IMAGE_NAME}:${env.BUILD_ID}"
                        
                        // 2. Adiciona a tag 'latest' à imagem recém-construída no Docker Hub (opcional, mas comum)
                        // Primeiro tagueia localmente a imagem construída com 'latest'
                        //sh "docker tag ${env.DOCKER_IMAGE_NAME}:${env.BUILD_ID} ${env.DOCKER_IMAGE_NAME}:latest"
                        // Depois, faz o push da tag 'latest' para o Docker Hub
                        //sh "docker push ${env.DOCKER_IMAGE_NAME}:latest"
                    }
                }
            }
        }
        stage('Deploy no Kubernetes') {
            environment {
                // Certifica-se de que tag_version é definida para uso no sed
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                withKubeConfig(credentialsId: 'kubeconfig') {
                    // Substitui a placeholder {{tag}} pela tag de versão (BUILD_ID) no deployment.yaml
                    sh "sed -i 's/{{tag}}/${tag_version}/g' ./k8s/deployment.yaml"
                    // Aplica o deployment atualizado no Kubernetes
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }
}