pipeline {
    agent any

    stages {
        stage('Checkout e Preparacao') {
            steps {
                git branch: 'main', credentialsId: 'SEU_ID_CREDENCIAIS_GIT', url: 'https://github.com/querinoz/file-transfer.git'
                sh 'echo "Conteudo do arquivo de dados transferido em $(date +%F)" > arquivo_a_enviar.dat'
            }
        }

        stage('Transferencia do Arquivo') {
            steps {
                script {
                    sh 'git config user.name "Jenkins Transfer Pipeline"'
                    sh 'git config user.email "jenkins-bot@example.com"'

                    try {
                        echo "Tentando adicionar, commitar e enviar (push) o novo arquivo."
                        
                        sh "git add arquivo_a_enviar.dat"
                        sh 'git commit -m "Transferencia: Adiciona arquivo via Pipeline Jenkins"'
                        
                        sh 'git push origin main' 
                        echo "Transferencia realizada com sucesso!"
                        
                    } catch (Exception e) {
                        echo "Falha no 'git push' (causa: ${e.getMessage()}). Criando LogFail.txt..."
                        
                        sh """
                            LOG_FILE="LogFail.txt"
                            DATE_TIME=\$(date '+%Y-%m-%d %H:%M:%S')
                            
                            echo "--- Falha na Transferencia em \$DATE_TIME ---" >> \$LOG_FILE
                            echo "O arquivo 'arquivo_a_enviar.dat' nao foi enviado com sucesso." >> \$LOG_FILE
                            echo "Causa provavel: Conflito de merge ou credenciais invalidas/expiradas." >> \$LOG_FILE
                            
                            git add \$LOG_FILE
                            git commit -m "LOG: Falha de transferencia registrada em \$DATE_TIME"
                            
                            git push origin main
                        """
                        echo "Log de falha (LogFail.txt) enviado com sucesso ao repositorio."
                        
                        throw e 
                    }
                }
            }
        }
    }
}
