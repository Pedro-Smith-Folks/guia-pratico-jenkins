pipeline {
    agent any //Define quem pode realizar essa automação

    stages { // Estagios de execução

        stage('Build Docker Image') { // Bloco de passos de execução
            steps {
                sh 'echo "Executando o comando Docker Build'

            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'echo "Executando o comando Docker push'
                
            }
        }

        stage('Deploy no Kubernetes') {
            steps {
                sh 'echo "Executando o comando kubectl apply'
                
            }
        }
    }
}
