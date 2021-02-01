//parses the git url to extract repo name 
def parseRepoName (git_url) {
  if(!git_url){
    throw new Error("git url ${git_url} is not valid.")
  }
  def (_,head,name) = (git_url =~ /^(git@|https:\/\/).*\/(.+)(\.git)+$/)[0]
  return name
}

//removes the origin/ from the branch name
def parseBranchName (git_branch) {
  if(!git_branch){
    throw new Error("branch ${git_branch} is not valid.")
  }
  def (_,name) = (git_branch =~ /^origin\/(.+)$/)[0]
  return name
}

//returns the deployment env name according to branch name
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

//returns environment name mapped to the git branch
def getMappedEnv (git_branch) {
  if(!git_branch){
    throw new Error("branch ${git_branch} is not valid.")
  }
  String bname = parseBranchName(git_branch) 
  String name;
  switch(bname) {
    case ~/develop/ : name = "dev"; break;
    case ~/release/: name = "qa"; break;
    case ~/prod/: name = "prod"; break;
    default: throw new Exception ("branch ${git_branch} not recognized.");
  }
  return name
}

//returns the type of the environment according to the branch name e.g sandbox or production
def getEnvType (git_branch) {
  if(!git_branch){
    throw new Error("branch ${git_branch} is not valid.")
  }
  String bname = parseBranchName(git_branch) 
  switch(bname) {
    case ~/(develop)|(release)/: return 'nonProd';
    case ~/master/: return "Prod"
    default: throw new Exception ("branch ${git_branch} not recognized.");
  }
}

pipeline {

  agent any

  tools { 
      maven 'Maven 3.6.3' 
      jdk 'jdk11' 
  }

  environment {
    PROJECT_NAME = parseRepoName(GIT_URL)
    MULE_ENV = getMappedEnv(GIT_BRANCH)
    MULE_ENCRYPTION_KEY = "${ANYPOINT_ENV_TYPE}.mule.encryption.key"
    ANYPOINT_ENV_TYPE = getEnvType(GIT_BRANCH)
    ANYPOINT_DEPLOYMENT_ENV = getDeployEnv(GIT_BRANCH)
    ANYPOINT_REGION = "eu-central-1" 
    ANYPOINT_WORKDERS = "1"
    ANYPOINT_WORKER_TYPE = "Micro"
    ANYPOINT_BG = "mboss"
    ANYPOINT_APP_CLIENT_ID = "anypoint_connectedApp.${ANYPOINT_ENV_TYPE}.client_id"
    ANYPOINT_APP_CLIENT_SECRET = "anypoint_connectedApp.${ANYPOINT_ENV_TYPE}.client_secret"
    }

  stages {

    stage ('Initialization') {
    steps {
      echo "GIT_BRANCH = $GIT_BRANCH"
      echo "PROJECT_NAME = $PROJECT_NAME"
      echo "ANYPOINT_ENV_TYPE = $ANYPOINT_ENV_TYPE"
      echo "ANYPOINT_DEPLOYMENT_ENV = $ANYPOINT_DEPLOYMENT_ENV"
      echo "MULE_ENV = $MULE_ENV"
      echo "MULE_ENCRYPTION_KEY = $MULE_ENCRYPTION_KEY"
      echo "ANYPOINT_APP_CLIENT_ID = $ANYPOINT_APP_CLIENT_ID"
      echo "ANYPOINT_APP_CLIENT_SECRET = $ANYPOINT_APP_CLIENT_SECRET"
      }
    }
    
    stage('Run Munit') {
      environment{
        MULE_ENCRYPTION_KEY = credentials("${MULE_ENCRYPTION_KEY}")
      }
      steps {
        configFileProvider([configFile(fileId: 'mvn-settings', variable: 'MAVEN_SETTINGS')]) {
          sh '''
            mvn -s $MAVEN_SETTINGS clean test \
              -Dmule.env=$MULE_ENV \
              -Dmule.key=$MULE_ENCRYPTION_KEY
          '''
        }
      }
    }

    stage('Deploy to Cloudhub') {
      environment{
        MULE_ENCRYPTION_KEY = credentials("${MULE_ENCRYPTION_KEY}")
        ANYPOINT_APP_CLIENT_ID = credentials("${ANYPOINT_APP_CLIENT_ID}")
        ANYPOINT_APP_CLIENT_SECRET = credentials("${ANYPOINT_APP_CLIENT_SECRET}")
      }

      steps {
        configFileProvider([configFile(fileId: 'mvn-settings', variable: 'MAVEN_SETTINGS')]) {
          sh '''
            mvn -s $MAVEN_SETTINGS deploy -DmuleDeploy  \
              -Dmule.env=$MULE_ENV \
              -Dmule.key=$MULE_ENCRYPTION_KEY \
              -DconnectedApp.clientId=$ANYPOINT_APP_CLIENT_ID \
              -DconnectedApp.clientSecret=$ANYPOINT_APP_CLIENT_SECRET \
              -DanypointEnvironment=$ANYPOINT_DEPLOYMENT_ENV \
              -Dworkers=$ANYPOINT_WORKDERS \
              -DworkerType=$ANYPOINT_WORKER_TYPE \
              -DbusinessGroup=$ANYPOINT_BG
          '''
        }
      }
    }
  }
}

