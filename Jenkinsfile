pipeline {
    agent any

    environment {
        SONAR_HOST_URL = "http://18.226.177.88:9000"
        NEXUS_REPO_URL = "http://18.226.177.88:8081/repository/lms/"
        DEPLOY_PATH = "/var/www/html"
    }

    stages {
        stage('Code Quality') {
            steps {
                echo 'Sonar Analysis Started'
                withCredentials([string(credentialsId: 'SONARQUBE_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh '''
                    cd webapp
                    sudo docker run --rm -e SONAR_HOST_URL="http://18.226.177.88:9000" \
                        -v "$PWD:/usr/src" -e SONAR_TOKEN="sqp_08a319a7a7bb32b7826c258d6c42d84c4ba936ea" \
                        sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms -Dsonar.sources=.
                    '''
                }
                echo 'Sonar Analysis Completed'
            }
        }

        stage('Build LMS') {
            steps {
                echo 'LMS Build Started'
                sh '''
                cd webapp
                npm install
                npm run build
                '''
                echo 'LMS Build Completed'
            }
        }

        stage('Publish LMS') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "Publishing LMS Version: ${packageJSONVersion}"

                    sh '''
                    cd webapp
                    zip -r lms-${packageJSONVersion}.zip dist
                    '''

                    withCredentials([usernamePassword(credentialsId: 'NEXUS_CREDENTIALS', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                        sh '''
                        curl -v -u admin:wasim123" \
                            --upload-file webapp/lms-${packageJSONVersion}.zip \
                            "http://18.226.177.88:8081/repository/lms"
                        '''
                    }
                }
            }
        }

        stage('Deploy LMS') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "Deploying LMS Version: ${packageJSONVersion}"

                    withCredentials([usernamePassword(credentialsId: 'NEXUS_CREDENTIALS', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                        sh '''
                        curl -u "admin:wasim123" \
                            -X GET "http://18.226.177.88:8081/repository/lms/lms-${packageJSONVersion}.zip" \
                            --output lms-${packageJSONVersion}.zip
                        '''
                    }

                    sh '''
                    sudo rm -rf /var/www/html/*
                    sudo unzip -o lms-${packageJSONVersion}.zip -d sudo cp -r webapp/dist/* /var/www/html/
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning Workspace'
            cleanWs()
        }
    }
}
