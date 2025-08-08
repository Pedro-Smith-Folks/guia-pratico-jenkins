pipeline {
    agent any //Define quem pode realizar essa automação
    environment {
        // Isso instrui o Jenkins a se conectar ao Docker através do Named Pipe do Windows.
        DOCKER_HOST = "npipe:////./pipe/docker_engine" 
    }

    stages { // Estagios de execução

        stage('Build Docker Image') {
            steps {
                sh 'echo "Executando o comando Docker Build"'
                script { // Plugins do Jenkins, abstraem as linhas de comando (Código Groovy)
                    dockerapp = docker.build("pedrofolks/folks-jenkins:${env.BUILD_ID}", '-f ./src/Dockerfile ./src') // Caminho para DockerFile, Contexto de build
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'echo "Executando o comando Docker push"'
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        dockerapp.push('latest') // tag //docker.image('pedrofolks/folks-jenkins:${env.BUILD_ID}', , '-f ./src/Dockerfile ./src').push()
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy no Kubernetes') {
            steps {
                sh 'echo "Executando o comando kubectl apply"'
                
            }
        }

    }
}