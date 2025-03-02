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
   }  
}