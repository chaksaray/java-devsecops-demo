pipeline {
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
        stage('Unit Tests - JUnit and Jacoco') {
          steps {
            echo 'unit test'
            sh "mvn test"
          }
        }

        stage('Mutation Tests - PIT') {
          steps {
            sh "mvn org.pitest:pitest-maven:mutationCoverage"
          }
        }

        stage('SonarQube - SAST') {
              steps {
                sh "Sonarqube scan"
                // sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-app -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_4ac6c7154a37970e77805fb8a628d07e2eb11a71"
              }
        }

        stage('Vulnerability Scan - Docker ') {
          steps {
            sh "mvn dependency-check:check"
          }
        }

        stage('Docker Build and Push') {
          steps {
            withDockerRegistry([credentialsId: "docker.hub", url: ""]) {
              sh 'printenv'
              sh 'docker build -t chaksaray/numeric-app:""$GIT_COMMIT"" .'
              sh 'docker push chaksaray/numeric-app:""$GIT_COMMIT""'
            }
          }
        }

        stage('Kubernetes Deployment - DEV') {
          steps {
            echo 'kubernetes deployed'
            // withKubeConfig([credentialsId: 'kubeconfig']) {
            //   sh "sed -i 's#replace#chaksaray/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
            //   sh "kubectl apply -f k8s_deployment_service.yaml"
            // }
          }
        }
    }

    post {
      always {
        junit 'target/surefire-reports/*.xml'
        jacoco execPattern: 'target/jacoco.exec'
        pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
        dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
      }

      // success {

      // }

      // failure {

      // }
  }
}
