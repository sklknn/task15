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
                    docker build -t sklknn/nginxssl ./nginx
                    docker build -t sklknn/apache ./apache
                '''
            }
        }
    }
}
