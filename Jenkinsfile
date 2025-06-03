pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("leandro282/guia-jenkins1:${env.BUILD_ID}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://registry.hub.docker.com", "dockerhub") {
                        docker.image("leandro282/guia-jenkins1:${env.BUILD_ID}").push("latest")
                        docker.image("leandro282/guia-jenkins1:${env.BUILD_ID}").push("${env.BUILD_ID}")
                    }
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
                    sh "kubectl apply -f k8s/deployment.yaml"
                }
            }
        }
    }
}
