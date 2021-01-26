pipeline {

    agent any

    environment {
        REGION = "eu-central-1" 
        WORKDERS = "1"
        WORKERTYPE= "Micro"
        BG = "mboss"
        DEPLOY_CREDS = credentials('connectedAppCredentials')
        MULE_SECRET_KEY = credentials('mule.key')

    }

    stages {
 
        stage('Run Munit') {
            steps {
                sh 'mvn test'
            }
        }
           

        stage('Deploy to Cloudhub') {
            steps {
        		configFileProvider([configFile(fileId: "${global-settings.xml}", variable: 'MAVEN_SETTINGS_XML')]) {
                sh '''-X -s $MAVEN_SETTINGS_XML deploy -DmuleDeploy \\
                    -Dmule.env=dev \\
                    -Dmule.key=${MULE_SECRET_KEY} \\
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
