pipeline {

  agent any
  
    environment {
	    DEPLOY_CREDS = credentials('connectedAppCredentials')
	    BG = "mboss"
	    WORKER = "Micro"
	    REGION = "eu-central-1"
  	}
  
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
            -DconnectedApp.clientId="%DEPLOY_CREDS_USR%" \\
            -DconnectedApp.clientSecret=%DEPLOY_CREDS_PSW%" \\
            -DanypointEnvironment=DEV \\
            -Dregion=%REGION% \\
            -Dworkers=1 \\
            -DworkerType=%WORKER% \\
            -DbusinessGroup=%BG%'''
      }
    }

  }
  tools {
    maven 'Maven'
  }
}