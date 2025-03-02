pipeline {
   agent any


   stages {
       stage('Code Quality') {
           steps {
               echo 'Sonar Analysis Started'
               sh 'cd webapp && sudo docker run --rm -e SONAR_HOST_URL="http://18.189.184.123:9000" -v ".:/usr/src" -e SONAR_TOKEN="sqa_4c7b8a5d7a8565c22085d6867b84a40cda0b6215" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms'
               echo 'Sonar Analysis Completed'
           }
       }
   }  
}