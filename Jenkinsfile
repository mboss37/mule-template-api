def getDeployEnv(git_branch) {
  if(!git_branch){
    throw new Error("branch ${git_branch} is not valid.")
  }
  String bname = parseBranchName(git_branch) 
  switch(bname) {
    case ~/develop/ : return "DEV";
    case ~/release/: return "QA";
    case ~/master/: return "PROD";
    default: throw new Exception ("branch ${git_branch} not recognized.");
  }
}

pipeline {

    agent any

    environment {
		ANYPOINT_ENV = getDeployEnv(GIT_BRANCH)
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

                sh '''mvn deploy  -DmuleDeploy \\
                    -Dmule.env=dev \\
                    -Dmule.key=${MULE_SECRET_KEY} \\
                    -DconnectedApp.clientId=${DEPLOY_CREDS_USR} \\
                    -DconnectedApp.clientSecret=${DEPLOY_CREDS_PSW} \\
                    -DanypointEnvironment=${ANYPOINT_ENV} \\
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
