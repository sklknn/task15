def remoteTest=[:]
remoteTest.name = 'test'
remoteTest.host = '158.160.66.170'
remoteTest.allowAnyHosts = true

pipeline {
    agent any

    enviroment {
        SSH_CREDS=credentials('53d35ffb-9b9a-47c0-8486-c9fd63317b8a')
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
                    remoteTest.user=env.SSH_CREDS_USR
                    remoteTest.identity=env.SSH_CREDS_PSW
                }
                sshCommand(remote: remoteTest, command: "ls -lah && woami")
                
            }
        }

        post {
            always {
                sleep 5
            }
        }
    }
}
