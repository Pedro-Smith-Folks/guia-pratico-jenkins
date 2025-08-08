pipeline {
    agent any //Define quem pode realizar essa automação

    tools {
        nodejs 'NodeJS-24' 
    }

    environment {
        // Isso instrui o Jenkins a se conectar ao Docker através do Named Pipe do Windows.
        DOCKER_HOST = "npipe:////./pipe/docker_engine" 
    }

    stages { // Estagios de execução

        stage('Testes') {
            steps {
                sh 'echo "Executando testes da aplicação"'
                // O Jenkins irá adicionar o Node.js ao PATH automaticamente
                sh 'node -v'
                sh 'npm -v'
                dir('src') {
                    sh 'npm install'
                    sh 'npm test'
                }
            }
        }

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
                        //docker.image('pedrofolks/folks-jenkins:${env.BUILD_ID}', , '-f ./src/Dockerfile ./src').push()
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy no Google Cloud') {
            steps {
                script {
                    // Autentica no Google Cloud usando a credencial do Jenkins
                    withCredentials([file(credentialsId: 'google-cloud-credentials', variable: 'gcloud_key')]) {
                        sh 'gcloud auth activate-service-account --key-file=${gcloud_key}'
                        
                        // Configura o kubectl para apontar para o seu cluster GKE
                        // Substitua 'SEU_PROJETO', 'SUA_ZONA' e 'SEU_CLUSTER' pelos valores corretos
                        sh 'gcloud container clusters get-credentials SEU_CLUSTER --zone SUA_ZONA --project SEU_PROJETO'
                        
                        sh 'echo "Realizando o deploy da aplicação"'
                        
                        // Atualiza o arquivo de deployment com a imagem correta
                        sh "sed -i 's|image: .*|image: pedrofolks/folks-jenkins:${env.BUILD_ID}|g' k8s/deployment.yaml"

                        // Aplica a configuração ao cluster
                        sh 'kubectl apply -f k8s/deployment.yaml'
                    }
                }
            }
        }

    }
}