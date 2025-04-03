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
                    docker build -t sklknn/nginxssl:${env.BUILD_ID} ./nginx
                    docker build -t sklknn/apache:${env.BUILD_ID} ./apache
                '''
            }
        }
    }
}
