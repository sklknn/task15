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
                    docker build -t sklknn/nginxssl:1.${BUILD_ID} -t sklknn/nginxssl:latest ./nginx
                    docker build -t sklknn/apache:1.${BUILD_ID} -t sklknn/nginxssl:latest ./apache
                    echo "DONE"
                '''
            }
        }
    }
}
