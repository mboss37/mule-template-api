

// Parses the git url to extract repo name 
def parseRepoName (git_url) {
  if(!git_url){
    throw new Error("git url ${git_url} is not valid.")
  }
  def (_,head,name) = (git_url =~ /^(git@|https:\/\/).*\/(.+)(\.git)+$/)[0]
  return name
}

//returns the deployment env name according to branch name
def getDeployEnv(git_branch) {
  if(!git_branch){
    throw new Error("branch ${git_branch} is not valid.")
  }
  String bname = parseBranchName(git_branch) 
  switch(bname) {
    case ~/dev/ : return "DEV";
    case ~/qa/: return "QA";
    case ~/uat/: return "UAT";
    default: throw new Exception ("branch ${git_branch} not recognized.");
  }
}

pipeline {

  agent any

  tools { 
      maven 'Maven 3.6.3' 
      jdk 'jdk8' 
  }

  environment {
    PROJECT_NAME = parseRepoName(GIT_URL)
    ANYPOINT_ENV = getDeployEnv(GIT_BRANCH)
    REGION = "eu-central-1" 
    WORKDERS = "1"
    WORKERTYPE= "Micro"
    BG = "mboss"
    DEPLOY_CREDS = credentials('connectedAppCredentials')
    MULE_SECRET_KEY = credentials('mule.key')
    }

  stages {

    stage ('Initialization') {
    steps {
      echo "PROJECT_NAME = $PROJECT_NAME"
      echo "ANYPOINT_ENV = $ANYPOINT_ENV"
      }
    }
    
    stage('Run Munit') {
      steps {
        configFileProvider([configFile(fileId: 'mvn-settings', variable: 'MAVEN_SETTINGS')]) {
        sh 'mvn -s $MAVEN_SETTINGS clean test'
        }
      }
    }

    // stage('Deploy to Cloudhub') {
    //   steps {
    //     configFileProvider([configFile(fileId: 'mvn-settings', variable: 'MAVEN_SETTINGS')]) {
    //       sh '''mvn -s $MAVEN_SETTINGS deploy -DmuleDeploy \\
    //       -Dmule.env=dev \\
    //       -Dmule.key=${MULE_SECRET_KEY} \\
    //       -DconnectedApp.clientId=${DEPLOY_CREDS_USR} \\
    //       -DconnectedApp.clientSecret=${DEPLOY_CREDS_PSW} \\
    //       -DanypointEnvironment=DEV \\
    //       -Dregion=${REGION} \\
    //       -Dworkers=${WORKDERS} \\
    //       -DworkerType=${WORKERTYPE} \\
    //       -DbusinessGroup=${BG}'''
    //     }
    //   }
    // }
  }
}

