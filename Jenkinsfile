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

    // --- SEÇÃO 'post' ADICIONADA AQUI ---
    post {
        always { // Esta seção 'always' será executada após todos os estágios, independentemente do sucesso ou falha
            echo 'Verificando o status da pipeline para um fato do Chuck Norris...'
            chuckNorris() // Este é o passo que o plugin Chuck Norris disponibiliza
        }
        success { // Esta seção é executada SE e SOMENTE SE a pipeline for um sucesso (todos os estágios passarem)
            echo 'A pipeline foi um sucesso! Chuck Norris aprova!'
        }
        failure { // Esta seção é executada SE e SOMENTE SE a pipeline falhar em algum estágio
            echo 'A pipeline falhou! O Chuck Norris está observando...'
        }
        // Você pode adicionar outras condições aqui se quiser, como:
        // unstable { // Ação se o build for instável (testes falham, mas o build principal passa)
        //     echo 'Build instável, mas Chuck Norris ainda pode salvar o dia.'
        //     chuckNorris()
        // }
    }
    // --- FIM DA SEÇÃO 'post' ---
}