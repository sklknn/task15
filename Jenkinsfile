def remoteTest=[:]
remoteTest.name = 'dev'
remoteTest.host = '158.160.76.220'
remoteTest.allowAnyHosts = true
remoteTest.user = 'sklknn'
/*
def remoteProd=[:]
remoteProd.name = 'production'
remoteProd.host = '158.160.80.155'
remoteProd.allowAnyHosts = true
remoteProd.user = 'sklknn'
*/
pipeline {
    agent any
 
    stages {
        stage('Build') {
            steps {
                echo 'Building images...'
                sh '''
                    docker build -t cr.yandex/crpagqn6vd2vbjstpe7f/nginxssl:1.${BUILD_ID}.dev -t cr.yandex/crpagqn6vd2vbjstpe7f/nginxssl:dev ./nginx
                    docker build -t cr.yandex/crpagqn6vd2vbjstpe7f/apache:1.${BUILD_ID}.dev -t cr.yandex/crpagqn6vd2vbjstpe7f/apache:dev ./apache
                '''
            }
        }

        stage('Push to yc container registry') {
            steps {
                echo 'Pushing images...'
                sh '''
                    docker push cr.yandex/crpagqn6vd2vbjstpe7f/nginxssl:1.${BUILD_ID}.dev
                    docker push cr.yandex/crpagqn6vd2vbjstpe7f/nginxssl:dev
                    
                    docker push cr.yandex/crpagqn6vd2vbjstpe7f/apache:1.${BUILD_ID}.dev
                    docker push cr.yandex/crpagqn6vd2vbjstpe7f/apache:dev
                '''
            }
        }
        
        stage('Deploy dev') {
            steps {
                echo 'Deploying to test env...'
                withCredentials([file(credentialsId: 'ssh_priv_key', variable: 'secretFile')]) {
                    // this works!!! 
                    script {
                        remoteTest.identityFile = env.secretFile
                    }
                    //run nginx container
                    catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                        // delete running container 
                        sshCommand(remote: remoteTest, command: 'sudo docker rm -f nginx')
                        // delete old image                         
                        sshCommand(remote: remoteTest, command: 'sudo docker rmi -f $(sudo docker images -af reference="cr.yandex/crpagqn6vd2vbjstpe7f/nginxssl" -q)')
                    }
                    // pull and run
                    sshCommand(remote: remoteTest, command: 'docker pull cr.yandex/crpagqn6vd2vbjstpe7f/nginxssl:dev', sudo: true)
                    sshCommand(remote: remoteTest, command: 'docker run -d --name nginx -p 443:443 -e PORT=443 -e DOLLAR=$ -e APACHE_URL=http://'+ remoteTest.host +':8080 cr.yandex/crpagqn6vd2vbjstpe7f/nginxssl:dev', sudo: true)
                    //run apache container
                    catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                        // delete running container 
                        sshCommand(remote: remoteTest, command: 'sudo docker rm -f apache')
                        // delete old image                         
                        sshCommand(remote: remoteTest, command: 'sudo docker rmi -f $(sudo docker images -af reference="cr.yandex/crpagqn6vd2vbjstpe7f/apache" -q)')
                    }
                    // pull and run
                    sshCommand(remote: remoteTest, command: 'docker pull cr.yandex/crpagqn6vd2vbjstpe7f/apache:dev', sudo: true)
                    sshCommand(remote: remoteTest, command: 'docker run -d --name apache -p 8080:8080 -e PORT=8080 -e DOLLAR=$ cr.yandex/crpagqn6vd2vbjstpe7f/apache:dev', sudo: true)

                }
            }
        }
/*    
        stage('Deploy prod') {
            steps {
                echo 'Deploying to production enviroment'
                withCredentials([file(credentialsId: 'ssh_priv_key', variable: 'secretFile')]) {
                    script {
                        remoteProd.identityFile = env.secretFile
                    }

                    //run nginx container
                    catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                        // delete running container 
                        sshCommand(remote: remoteProd, command: 'sudo docker rm -f $(sudo docker ps -aqf "name=nginx")')
                        // delete old image                         
                        sshCommand(remote: remoteProd, command: 'sudo docker rmi -f $(sudo docker images -af reference="cr.yandex/crp0n9cjqc11aftmre79/nginxssl" -q)')
                    }
                    // pull and run
                    sshCommand(remote: remoteProd, command: 'docker pull cr.yandex/crp0n9cjqc11aftmre79/nginxssl:latest', sudo: true)
                    sshCommand(remote: remoteProd, command: 'docker run -d --name nginx -p 443:443 -e PORT=443 -e DOLLAR=$ -e APACHE_URL=http://'+ remoteProd.host +':8080 cr.yandex/crp0n9cjqc11aftmre79/nginxssl', sudo: true)
                    //run apache container
                    catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                        // delete running container 
                        sshCommand(remote: remoteProd, command: 'sudo docker rm -f $(sudo docker ps -aqf "name=apache")')
                        // delete old image                         
                        sshCommand(remote: remoteProd, command: 'sudo docker rmi -f $(sudo docker images -af reference="cr.yandex/crp0n9cjqc11aftmre79/apache" -q)')
                    }
                    // pull and run
                    sshCommand(remote: remoteProd, command: 'docker pull cr.yandex/crp0n9cjqc11aftmre79/apache:latest', sudo: true)
                    sshCommand(remote: remoteProd, command: 'docker run -d --name apache -p 8080:8080 -e PORT=8080 -e DOLLAR=$ cr.yandex/crp0n9cjqc11aftmre79/apache', sudo: true)
                }
            }
        }
    */
    }
}
