def remoteTest=[:]
remoteTest.name = 'test'
remoteTest.host = '158.160.66.170'
remoteTest.allowAnyHosts = true

pipeline {
    agent any

    environment {
        SSH_CREDS=credentials('ssh_priv_key')
    }

    stages {
        
        stage('verify Docker installed') {
            steps {
                sh 'docker --version'
            }
        }

        stage('Build') {
            steps {
                echo 'Building images...'
                sh '''
                    docker build -t cr.yandex/crp0n9cjqc11aftmre79/nginxssl:1.${BUILD_ID} -t cr.yandex/crp0n9cjqc11aftmre79/nginxssl:latest ./nginx
                    docker build -t cr.yandex/crp0n9cjqc11aftmre79/apache:1.${BUILD_ID} -t cr.yandex/crp0n9cjqc11aftmre79/apache:latest ./apache
                '''
            }
        }

        stage('Push to yc container registry') {
            steps {
                echo 'Pushing images...'
                sh '''
                    docker push cr.yandex/crp0n9cjqc11aftmre79/nginxssl:1.${BUILD_ID}
                    docker push cr.yandex/crp0n9cjqc11aftmre79/nginxssl:latest
                    
                    docker push cr.yandex/crp0n9cjqc11aftmre79/apache:1.${BUILD_ID}
                    docker push cr.yandex/crp0n9cjqc11aftmre79/apache:latest
                '''
            }
        }
        
        stage('Deploy test') {
            steps {
                script {
                    remoteTest.user=sklknn
                    remoteTest.identityFile=env.SSH_CREDS
                }
                
                sshCommand(remote: remoteTest, command: "ls -lah && woami")
                
            }
        }
    }
    post {
        always {
            sleep 5
        }
    }
}
