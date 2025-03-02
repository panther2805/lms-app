pipeline {
   agent any


   stages {
       stage('Code Quality') {
           steps {
               echo 'Sonar Analysis Started'
               sh 'cd webapp && sudo docker run --rm -e SONAR_HOST_URL="http://99.79.33.138:9000" -v ".:/usr/src" -e SONAR_TOKEN="sqa_6a936b2412e3a2e452517f54e381ca7cb3572c0f" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms-app'
               echo 'Sonar Analysis Completed'
           }
       }
        
        stage('Build LMS') {
           steps {
               echo 'LMS Build Started'
               sh 'cd webapp && npm install && npm run build'
               echo 'LMS Build Completed'
           }
       }
      
       stage('Publish LMS') {
           steps {
               script {
                   def packageJson = readJSON file: 'webapp/package.json'
                   def packageJSONVersion = packageJson.version
                   echo "${packageJSONVersion}"
                   sh "zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                   sh "curl -v -u admin:wasim123 --upload-file webapp/lms-${packageJSONVersion}.zip http://99.79.33.138:8081/repository/lms-app/"
               }
           }
       }
      
       stage('Deploy LMS') {
           steps {
               script {
                   def packageJson = readJSON file: 'webapp/package.json'
                   def packageJSONVersion = packageJson.version
                   echo "${packageJSONVersion}"
                   sh "curl -u admin:wasim123 -X GET \'http://99.79.33.138:8081/repository/lms-app/lms-${packageJSONVersion}.zip\' --output lms-'${packageJSONVersion}'.zip"
                   sh 'sudo rm -rf /var/www/html/*'
                   sh "sudo unzip -o lms-'${packageJSONVersion}'.zip"
                   sh "sudo cp -r webapp/dist/* /var/www/html"
               }
           }
       }
   }
}