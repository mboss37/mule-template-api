pipeline {

    agent any

    environment {

        REGION = "=eu-central-1" 
        WORKDERS = "1"
        WORKERTYPE= "Micro"
        BG = "mboss"

    }

    stage('Run Munit') {

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
            -Dregion=${REGION} \\
            -Dworkers=${WORKDERS} \\
            -DworkerType=${WORKERTYPE} \\
            -DbusinessGroup=${BG}'''

      }

    }

  tools {
      
    maven 'Maven'

  }

}