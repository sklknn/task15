pipeline {
    agent any

    stages {
        
        stage('verify Docker installed') {
            steps {
                sh 'docker --version'
            }
        }

        stage('Build') {
            steps {
                sh '''
                    echo "Building images"

                    docker build -t cr.yandex/crp0n9cjqc11aftmre79/nginxssl:1.${BUILD_ID} -t cr.yandex/crp0n9cjqc11aftmre79/nginxssl:latest ./nginx
                    docker build -t cr.yandex/crp0n9cjqc11aftmre79/apache:1.${BUILD_ID} -t cr.yandex/crp0n9cjqc11aftmre79/apache:latest ./apache
                '''
            }
        }

        stage('Push to yc container registry'){
            steps {
                sh '''
                    docker push cr.yandex/crp0n9cjqc11aftmre79/nginxssl:1.${BUILD_ID}
                    docker push cr.yandex/crp0n9cjqc11aftmre79/nginxssl:latest
                    
                    docker push cr.yandex/crp0n9cjqc11aftmre79/apache:1.${BUILD_ID}
                    docker push cr.yandex/crp0n9cjqc11aftmre79/apache:latest
                '''
            }
        }
    }
}
