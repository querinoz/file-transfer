pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/querinoz/file-transfer.git'
        BRANCH = 'main'
    }

    stages {

        stage('Checkout e Preparacao') {
            steps {
                echo "Fazendo checkout do repositório..."
                git branch: "${BRANCH}",
                    url: "${REPO_URL}",
                    credentialsId: 'github-token'   // ID criado nas credenciais do Jenkins
            }

            post {
                failure {
                    echo "Falha ao fazer checkout do repositório."
                }
            }
        }

        stage('Gerar Arquivo') {
            steps {
                script {
                    def dataAtual = sh(script: "date +%F", returnStdout: true).trim()
                    sh """
                        echo 'Conteúdo do arquivo gerado em ${dataAtual}' > arquivo_a_enviar.dat
                    """
                }
            }
        }

