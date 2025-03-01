pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo "Run Builds"
                sh 'cat /etc/os-release'
            }
        }
        stage('Test') {
            steps {
                echo "Run Tests"
            }
        }
        stage('Deploy') {
            steps {
                echo "Run Deployment"
            }
        }
    }
}