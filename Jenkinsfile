pipeline {
  agent any
  environment {
    ANYPOINT_DEPLOYMENT_ENV = "Dev"
    ANYPOINT_REGION = "eu-central-1"
    ANYPOINT_WORKDERS = "1"
    ANYPOINT_WORKER_TYPE = "Micro"
    ANYPOINT_BG = "harvey-nichols-6"
    ANYPOINT_APP_CLIENT_ID = "anypoint_connectedApp.nonProd.client_id"
    ANYPOINT_APP_CLIENT_SECRET = "anypoint_connectedApp.nonProd.client_secret"
  }

  stages {
    stage('---Initialization---') {
    steps {
    echo "ANYPOINT_DEPLOYMENT_ENV = $ANYPOINT_DEPLOYMENT_ENV"
    echo "ANYPOINT_REGION = $ANYPOINT_REGION"
    echo "ANYPOINT_WORKDERS = $ANYPOINT_WORKDERS"
    echo "ANYPOINT_WORKER_TYPE = $ANYPOINT_WORKER_TYPE"
    echo "ANYPOINT_BG = $ANYPOINT_BG"
    echo "ANYPOINT_APP_CLIENT_ID = $ANYPOINT_APP_CLIENT_ID"
    echo "ANYPOINT_APP_CLIENT_SECRET = $ANYPOINT_APP_CLIENT_SECRET"
  }

  }
    stage('---Run MUnit---') {
      steps {
      echo "Running MUnit..."
      }
    }

    stage('---Deployment---') {
      environment {
        ANYPOINT_APP_CLIENT_ID = credentials("${ANYPOINT_APP_CLIENT_ID}")
        ANYPOINT_APP_CLIENT_SECRET = credentials("${ANYPOINT_APP_CLIENT_SECRET}")
      }
      steps {
      sh '''mvn deploy -DmuleDeploy \
            -DskipMunitTests \
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