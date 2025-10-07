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

        stage('Commit e Push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-token', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                        sh """
                            git config user.name "Jenkins Transfer Pipeline"
                            git config user.email "jenkins-bot@example.com"
                            git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/querinoz/file-transfer.git

                            echo "Adicionando e enviando arquivo..."
                            git add arquivo_a_enviar.dat || true
                            git commit -m "Transferencia: Adiciona arquivo via Pipeline Jenkins" || echo "Nada para commitar"
                            git push origin ${BRANCH}
                        """
                    }
                }
            }

            post {
                failure {
                    echo "❌ Falha ao fazer o push. Verifique as credenciais ou conflitos no Git."
                    sh '''
                        LOG_FILE=LogFail.txt
                        DATE_TIME=$(date "+%Y-%m-%d %H:%M:%S")
                        echo "--- Falha na Transferencia em $DATE_TIME ---" > $LOG_FILE
                        echo "Causa: Falha no push (provável credenciais ou conflito)." >> $LOG_FILE
                        git add $LOG_FILE
                        git commit -m "LOG: Falha de transferencia registrada em $DATE_TIME" || true
                    '''
                }
                success {
                    echo "✅ Arquivo enviado com sucesso ao GitHub!"
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finalizado."
        }
    }
}