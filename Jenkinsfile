pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean -DskipTests package'
      }
    }

    stage('Test') {
      steps {
        sh 'mvn test'
      }
    }

    stage('Deploy to Cloudhub') {
      steps {
        sh 'mvn deploy -Dmule.env=dev -Dmule.key=mulesoftmulesoftmulesoft'
      }
    }

  }
  tools {
    maven 'Maven'
  }
}