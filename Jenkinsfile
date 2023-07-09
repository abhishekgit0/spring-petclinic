pipeline {
  agent any
  stages {
    stage('Compile') {
      steps {
        sh './mvnw clean compile'
      }
    }

    stage('Static Analysis') {
      steps {
        sh '''./mvnw sonar:sonar \\
  -Dsonar.projectKey=Petclinic \\
  -Dsonar.projectName=\'Petclinic\' \\
  -Dsonar.host.url=http://3.110.235.0:9000 \\
  -Dsonar.token=sqp_51ddf0cdda92d6c17e01efc61d65fc3485bb76c8'''
      }
    }

    stage('Unit Test') {
      steps {
        sh './mvnw "-Dtest=**/petclinic/*/*.java" test'
      }
    }

    stage('Package') {
      steps {
        sh './mvnw package -DskipTests=true'
      }
    }

    stage('Deploy') {
      parallel {
        stage('Deploy') {
          agent {
            node {
              label 'test'
            }

          }
          steps {
            sh './mvnw spring-boot:run </dev/null &>/dev/null &'
          }
        }

        stage('Integration and Performance Tests') {
          agent {
            node {
              label 'test'
            }

          }
          steps {
            sh './mvnw verify -DskipTests=true'
            junit '**/target/surefire-reports/TEST-*.xml'
          }
        }

      }
    }

  }
}