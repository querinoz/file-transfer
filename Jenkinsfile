pipeline {
    agent any // Executa em qualquer agente disponível do Jenkins

    stages {
        stage('Checkout e Preparação') {
            steps {
                // 1. Clonar o Repositório Alvo
                // O Jenkins baixa o estado atual do repositório para o workspace.
                git branch: 'main', credentialsId: 'SEU_ID_CREDENCIAIS_GIT', url: 'https://github.com/querinoz/file-transfer.git'
                
                // 2. Simular/Preparar o arquivo a ser transferido
                // Este passo simula a cópia do seu arquivo "local" (fora do workspace) 
                // para o diretório de trabalho do Jenkins.
                sh 'echo "Conteudo do arquivo de dados transferido em $(date +%F)" > arquivo_a_enviar.dat'
            }
        }

        stage('Transferência do Arquivo (Push Condicional)') {
            steps {
                // Usamos o bloco 'script' para permitir a lógica try-catch do Groovy
                script {
                    // Configurações do Git para o commit
                    sh 'git config user.name "Jenkins Transfer Pipeline"'
                    sh 'git config user.email "jenkins-bot@example.com"'

                    // Tentar o Push do Arquivo
                    try {
                        echo "Tentando adicionar, commitar e enviar (push) o novo arquivo."
                        
                        // Adicionar e commitar a alteração
                        sh "git add arquivo_a_enviar.dat"
                        sh 'git commit -m "Transferencia: Adiciona arquivo via Pipeline Jenkins"'
                        
                        // Fazer o Push para o branch principal
                        sh 'git push origin main' 
                        echo "Transferência realizada com sucesso!"
                        
                    } catch (Exception e) {
                        // === Bloco EXECUTADO SE o push FALHAR ===
                        echo "Falha no 'git push' (causa: ${e.getMessage()}). Criando LogFail.txt..."
                        
                        // 3. Criar e preencher o LogFail.txt
                        sh """
                            LOG_FILE="LogFail.txt"
                            DATE_TIME=\$(date '+%Y-%m-%d %H:%M:%S')
                            
                            echo "--- Falha na Transferência em \$DATE_TIME ---" >> \$LOG_FILE
                            echo "O arquivo 'arquivo_a_enviar.dat' nao foi enviado com sucesso." >> \$LOG_FILE
                            echo "Causa provavel: Conflito de merge ou credenciais invalidas/expiradas." >> \$LOG_FILE
                            
                            # 4. Commitar e Enviar (Push) o Log de Falha
                            git add \$LOG_FILE
                            git commit -m "LOG: Falha de transferencia registrada em \$DATE_TIME"
                            
                            // Tentativa de Push do Log: Este push será mais simples e deve ter sucesso.
                            git push origin main
                        """
                        echo "Log de falha (LogFail.txt) enviado com sucesso ao repositorio."
                        
                        // Re-lança a exceção para que o build do Jenkins seja marcado como 'FAILURE'
                        throw e 
                    }
                }
            }
        }
    }
