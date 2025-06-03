pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("leandro282/guia-jenkins1:${env.BUILD_ID}", "./src")
                    env.DOCKER_IMAGE_NAME = "leandro282/guia-jenkins1"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // **BLOCO DE DEBUG - CUIDADO COM A SEGURANÇA DA SENHA NO LOG**
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        // Faz o login explicitamente usando as variáveis de ambiente das credenciais do Jenkins
                        sh "echo \"$DOCKER_PASSWORD\" | docker login -u \"$DOCKER_USERNAME\" --password-stdin https://registry.hub.docker.com"
                        
                        // Faz o push da imagem com a tag BUILD_ID
                        sh "docker push ${env.DOCKER_IMAGE_NAME}:${env.BUILD_ID}"
                        
                        // Opcional: Adiciona a tag 'latest' e faz o push, se a primeira passar
                        sh "docker tag ${env.DOCKER_IMAGE_NAME}:${env.BUILD_ID} ${env.DOCKER_IMAGE_NAME}:latest"
                        sh "docker push ${env.DOCKER_IMAGE_NAME}:latest"
                    }
                    // **FIM DO BLOCO DE DEBUG**
                }
            }
        }
        stage('Deploy no Kubernetes') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                withKubeConfig(credentialsId: 'kubeconfig') {
                    sh "sed -i 's/{{tag}}/${tag_version}/g' ./k8s/deployment.yaml"
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }
}