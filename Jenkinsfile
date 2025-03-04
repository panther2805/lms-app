pipeline {
    agent any

    environment {
        DOCKER_USER = credentials('panther6')  // Store in Jenkins Credentials
        DOCKER_PASS = credentials('Shubhu@2846')
        panther6 = "panther6/lms-frontend"
    }

    stages {
        stage('Build LMS') {
            steps {
                echo 'Building LMS Frontend...'
                sh '''
                    cd webapp
                    npm install
                    npm run build
                '''
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "LMS Version: ${packageJSONVersion}"

                    sh """
                        docker build -t ${panther6}:${packageJSONVersion} webapp
                        docker tag ${panther6}:${packageJSONVersion} ${panther6}:latest
                        echo "Shubhu@2846" | docker login -u "panther6" --password-stdin
                        docker push ${panther6}:${packageJSONVersion}
                        docker push ${panther6}:latest
                    """
                }
            }
        }

        stage('Deploy LMS') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "Deploying LMS Version: ${packageJSONVersion}"

                    sh """
                        docker pull ${panther6}:${packageJSONVersion}
                        docker stop lms-frontend || true
                        docker rm lms-frontend || true
                        docker run -d -p 3000:3000 --name lms-frontend ${panther6}:${packageJSONVersion}
                    """
                }
            }
        }
    }
}
