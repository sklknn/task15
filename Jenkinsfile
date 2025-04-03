pipeline {
    agent any

    stages {
        stage('verify Docker installed') {
            steps {
                sh 'docker --version'
                sh 'docker run hello-world'
            }
        }
    }
}
