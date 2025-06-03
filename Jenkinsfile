pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Constrói a imagem e já a taggeia com o BUILD_ID
                    docker.build("leandro282/guia-jenkins1:${env.BUILD_ID}", "./src")
                    // Define uma variável de ambiente para o nome base da imagem, útil para o push
                    // Isso não captura o objeto da imagem, apenas o nome da string
                    env.DOCKER_IMAGE_NAME = "leandro282/guia-jenkins1"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Assegura que você está autenticado no Docker Hub
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        // Faz o push da imagem com a tag BUILD_ID
                        docker.image("${env.DOCKER_IMAGE_NAME}:${env.BUILD_ID}").push() // Faz o push com a tag existente

                        // Opcional: Para fazer o push da mesma imagem com a tag 'latest'
                        // Primeiro, você pode taggear localmente a imagem construída
                        docker.image("${env.DOCKER_IMAGE_NAME}:${env.BUILD_ID}").addTag('latest')
                        // Em seguida, faz o push da tag 'latest'
                        docker.image("${env.DOCKER_IMAGE_NAME}:latest").push()
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