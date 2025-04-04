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
                    ls -la
                    docker build -t cr.yandex/crp0n9cjqc11aftmre79/nginxssl:1.${BUILD_ID} -t cr.yandex/crp0n9cjqc11aftmre79/nginxssl:latest ./nginx
                    docker build -t cr.yandex/crp0n9cjqc11aftmre79/apache:1.${BUILD_ID} -t cr.yandex/crp0n9cjqc11aftmre79/apache:latest ./apache
                    echo "DONE"
                '''
            }
        }
    }
}
