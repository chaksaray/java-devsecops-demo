pipeline {
  agent any
  tools {
      maven 'Maven 3.6.3'
  }
  stages {
        stage('Build Artifact') {
              steps {
                echo 'artifact builded'
                sh "mvn clean package -DskipTests=true"
                archive 'target/*.jar' //so that they can be downloaded later
                // archive 'target*//*.jar'
              }
          }
        stage('Unit Tests - JUnit and Jacoco') {
          steps {
            echo 'unit test'
            sh "mvn test"
          }
          post {
            always {
              junit 'target/surefire-reports/*.xml'
              jacoco execPattern: 'target/jacoco.exec'
            }
          }
        }

        stage('Mutation Tests - PIT') {
          steps {
            sh "mvn org.pitest:pitest-maven:mutationCoverage"
          }
          post {
            always {
              pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
            }
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
}
