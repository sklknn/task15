def remoteTest=[:]
remoteTest.name = 'test'
remoteTest.host = '158.160.66.170'
remoteTest.allowAnyHosts = true
remoteTest.user = 'sklknn'

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
                withCredentials([file(credentialsId: 'ssh_priv_key', variable: 'secretFile')]) {
                    // this works
                    sh 'cat $secretFile'
                    script {
                        remoteTest.identityFile = env.secretFile
                    }
                    sshCommand(remote: remoteTest, command: "ls -lah && woami")
                        //env.remoteTest.identityFile = env.secretFile
                }
                //script {
                //    remoteTest.user=sklknn
                //    remoteTest.identityFile=env.SSH_CREDS
                //}
                
                
                
            }
        }
    }
    post {
        always {
            sleep 5
        }
    }
}
