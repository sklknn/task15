def remoteTest=[:]
remoteTest.name = 'test'
remoteTest.host = '158.160.66.170'
remoteTest.allowAnyHosts = true
remoteTest.user = 'sklknn'

def remoteProd=[:]
remoteProd.name = 'production'
remoteProd.host = '192.168.0.1'
remoteProd.allowAnyHosts = true
remoteProd.user = 'sklknn'

pipeline {
    agent any
 
    stages {
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
        
        stage('Deploy in test') {
            steps {
                withCredentials([file(credentialsId: 'ssh_priv_key', variable: 'secretFile')]) {
                    // this works!!! 
                    script {
                        remoteTest.identityFile = env.secretFile
                    }
                    //run nginx container
                    
                    // delete running container sudo docker rm -f $(sudo docker ps -aqf "name=nginx")
                    sshCommand(remote: remoteTest, command: 'sudo docker rm -f $(sudo docker ps -aqf "name=nginx")')
                    // delete old image sudo docker rmi $(sudo docker images -af reference='cr.yandex/crp0n9cjqc11aftmre79/nginxssl' -q)
                    sshCommand(remote: remoteTest, command: 'sudo docker rmi $(sudo docker images -af reference="cr.yandex/crp0n9cjqc11aftmre79/nginxssl" -q)')
                    // pull and run
                    sshCommand(remote: remoteTest, command: 'docker pull cr.yandex/crp0n9cjqc11aftmre79/nginxssl:latest', sudo: true)
                    sshCommand(remote: remoteTest, command: 'docker run -d --name nginx -p 443:443 -e PORT=443 -e DOLLAR=$ -e APACHE_URL=http://localhost:8080 cr.yandex/crp0n9cjqc11aftmre79/nginxssl', sudo: true)
                    //run apache container

                }
            }
        }
    }
    post {
        always {
            sleep 5
        }
    }
}
