pipeline {
  agent any
  agent any
    tools {
        maven 'Maven 3.6.3'
    }
  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }   
    }
}
