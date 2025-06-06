pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    dockerapp = docker.build("leandro282/guia-jenkins1:${env.BUILD_ID}", "-f ./src/Dockerfile ./src")
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
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh "sed -i 's|{{tag}}|${tag_version}|g' ./k8s/deployment.yaml"
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }

    // --- SEÇÃO 'post' AJUSTADA E IDENTADA CORRETAMENTE ---
    post {
        always {
            // Chuck Norris aparece em todos os builds
            chuckNorris() // Este passo depende do plugin Chuck Norris estar instalado no Jenkins
        }

        success {
            echo '🚀 Deploy realizado com sucesso!'
            echo '💪 Chuck Norris aprova seu pipeline DevSecOps!'
            echo "✅ Imagem jamalshadowdev/meuapp:${env.BUILD_ID} deployada no Kubernetes"
            // Se você quiser que o Chuck Norris apareça APENAS no sucesso, mova chuckNorris() para cá
        }

        failure {
            echo '❌ Build falhou, mas Chuck Norris nunca desiste!'
            echo '🔍 Chuck Norris está investigando o problema...'
            echo '💡 Verifique: Docker build, DockerHub push ou Kubernetes deploy'
            // Se você quiser que o Chuck Norris apareça APENAS na falha, mova chuckNorris() para cá
        }

        unstable {
            echo '⚠️ Build instável - Chuck Norris está monitorando'
            // Se você quiser que o Chuck Norris apareça APENAS no instável, mova chuckNorris() para cá
        }
    }
    // --- FIM DA SEÇÃO 'post' ---
}