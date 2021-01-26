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
        sh '''mvn deploy  -DmuleDeploy \\
            -Dmule.env=dev \\
            -Dmule.key=mulesoftmulesoftmulesoft \\
            -DconnectedApp.clientId=c7b4699bb5924fc3b77c5fae1b75968a \\
            -DconnectedApp.clientSecret=e4df4135452D4F5bB3fd688aCA86aF24 \\
            -DanypointEnvironment=DEV \\
            -Dregion=eu-central-1 \\
            -Dworkers=1 \\
            -DworkerType=Micro \\
            -DbusinessGroup=mboss'''
      }
    }

  }
  tools {
    maven 'Maven'
  }
}