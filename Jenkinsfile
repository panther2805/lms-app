pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning LMS repository...'
                git branch: 'eks', url: 'https://github.com/panther2805/lms-app.git'
            }
        }

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

                    sh '''
                        docker build -t panther6/lms-frontend:${packageJSONVersion} .
                        docker tag panther6/lms-frontend:${packageJSONVersion} panther6/lms-frontend:latest
                        docker login -u panther6 -p Shubhu@2846
                        docker push panther6/lms-frontend:${packageJSONVersion}
                        docker push panther6/lms-frontend:latest
                    '''
                }
            }
        }

        stage('Deploy LMS') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "Deploying LMS Version: ${packageJSONVersion}"

                    sh '''
                        docker pull panther6/lms-frontend:${packageJSONVersion}
                        docker stop lms-frontend || true
                        docker rm lms-frontend || true
                        docker run -d -p 80:3000 --name lms-frontend panther6/lms-frontend:${packageJSONVersion}
                    '''
                }
            }
        }
    }
}
