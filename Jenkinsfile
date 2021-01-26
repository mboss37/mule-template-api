pipeline {

    agent any

    environment {

        REGION = "=eu-central-1" 
        WORKDERS = "1"
        WORKERTYPE= "Micro"
        BG = "mboss"
        DEPLOY_CREDS = credentials('connectedAppCredentials')

    }

    stages {

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
                    -DconnectedApp.clientId=${DEPLOY_CREDS_USR} \\
                    -DconnectedApp.clientSecret=${DEPLOY_CREDS_PSW} \\
                    -DanypointEnvironment=DEV \\
                    -Dregion=${REGION} \\
                    -Dworkers=${WORKDERS} \\
                    -DworkerType=${WORKERTYPE} \\
                    -DbusinessGroup=${BG}'''

            }

        }
    }

  tools {
      
    maven 'Maven'

  }

}
