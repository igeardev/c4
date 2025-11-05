pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@10.0.0.116"
        DEPLOY_PATH = "/opt/release"
        SSH_KEY = "/opt/dev_web_c4registro.key"
    }

    stages {
        stage('Checkout') {
            steps {
                echo '📦 Baixando código do GitHub...'
                git branch: 'main', url: 'https://github.com/igeardev/c4.git'
            }
        }

        stage('Build') {
            steps {
                echo '🧱 Preparando arquivo WAR...'
                sh '''
                mkdir -p target
                if [ -f petclinic.war ]; then
                    cp petclinic.war target/app.war
                elif ls target/*.war >/dev/null 2>&1; then
                    cp target/*.war target/app.war
                else
                    echo "Nenhum WAR encontrado!"; exit 1
                fi
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Enviando WAR para servidor remoto...'
                sh '''
                TIMESTAMP=$(date +"%Y-%m-%d_%H-%M-%S")
                WAR_NAME="app_${TIMESTAMP}.war"

                if [ ! -f $SSH_KEY ]; then
                    echo "❌ ERRO: Chave SSH não encontrada em $SSH_KEY"
                    exit 1
                fi

                chmod 600 $SSH_KEY

                echo "➡️ Copiando $WAR_NAME para $DEPLOY_SERVER:$DEPLOY_PATH"
                scp -o StrictHostKeyChecking=no -i $SSH_KEY target/app.war $DEPLOY_SERVER:$DEPLOY_PATH/$WAR_NAME

                echo "🔁 Reiniciando Tomcat (se existir)..."
                ssh -o StrictHostKeyChecking=no -i $SSH_KEY $DEPLOY_SERVER "sudo systemctl restart tomcat9 || echo 'Tomcat não encontrado, apenas cópia realizada.'"

                echo "✅ Deploy finalizado: $DEPLOY_PATH/$WAR_NAME"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deploy realizado com sucesso!'
        }
        failure {
            echo '❌ Erro no deploy, verifique os logs do Jenkins.'
        }
    }
}
