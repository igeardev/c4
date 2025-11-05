pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@10.0.0.116"
        DEPLOY_PATH = "/opt/release"
        SSH_KEY = "~/.ssh/dev_web_c4registro.key"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Baixando código do GitHub...'
                git branch: 'main', url: 'https://github.com/igeardev/c4.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Compilando projeto (ou copiando WAR existente)...'
                sh 'mkdir -p target && cp petclinic.war target/app.war'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Enviando WAR para servidor remoto...'
                sh '''
                chmod 600 $SSH_KEY
                scp -i $SSH_KEY target/app.war $DEPLOY_SERVER:$DEPLOY_PATH/
                                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deploy realizado com sucesso!'
        }
        failure {
            echo '❌ Erro no deploy, verifique os logs.'
        }
    }
}
