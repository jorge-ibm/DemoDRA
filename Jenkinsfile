#!groovy
/*
    Sample weather app that deploys to cloud foundry
 */

pipeline {
    agent any
    environment {
        // You need to specify 4 required environment variables first, they are going to be used for the following IBM Cloud DevOps steps
        IBM_CLOUD_DEVOPS_API_KEY = credentials('sample-apikey-weather-app')
        IBM_CLOUD_DEVOPS_APP_NAME = 'Weather-App-Demo-Sample'

    }
    parameters {
      string(name: 'username', defaultValue: 'jorge.rangel@ibm.com', description: 'Cloud Foundry username')
      string(name: 'organization', defaultValue: 'jorge.rangel@ibm.com', description: 'Cloud Foundry organization')
    }
    stages {
      stage('Build') {
        steps {
          buildImage()
        }
      }
      stage('Unit Test and Code Coverage') {
        steps {
          unitTest()
        }
      }
      stage('Deploy to Staging') {
        steps {
          deployStaging(IBM_CLOUD_DEVOPS_APP_NAME, ${params.organization}, 'dev', IBM_CLOUD_DEVOPS_API_KEY)
        }
      }

      stage('Deploy to Prod') {
        steps {
          deployProd(IBM_CLOUD_DEVOPS_APP_NAME, ${params.organization}, 'dev', IBM_CLOUD_DEVOPS_API_KEY)
        }
      }
    }
}
def buildImage() {
  sh '''
  #!/bin/bash +x

  echo -e "Building container image"
  export PATH=/opt/IBM/node-v4.2/bin:$PATH
  npm install
  grunt dev-setup --no-color
  '''
}
def unitTest() {
  sh '''
  #!/bin/bash
  export PATH=/opt/IBM/node-v4.2/bin:$PATH
  npm install
  npm install -g grunt-idra3

  set +e
  grunt dev-test-cov --no-color
  grunt_result=$?
  set -e

  if [ $grunt_result -ne 0 ]; then
   exit $grunt_result
  fi
  '''
}
def deployStaging(appName, organization, space, apiKey) {
  sh '''
  #!/bin/bash
  # Push app
  export CF_APP_NAME="staging-$appName"

  cf api https://api.ng.bluemix.net
  cf login -u apikey -p $apiKey -o $organization -s $space
  cf push "${CF_APP_NAME}"
  export APP_URL=http://$(cf app $CF_APP_NAME | grep -e urls: -e routes: | awk '{print $2}')
  # View logs
  #cf logs "${CF_APP_NAME}" --recent
  '''
}
def deployProd(appName, organization, space, apiKey) {
  sh '''
  #!/bin/bash
  # Push app
  export CF_APP_NAME="$appName"
  cf push "${CF_APP_NAME}"
  export APP_URL=http://$(cf app $CF_APP_NAME | grep -e urls: -e routes: | awk '{print $2}')
  # View logs
  #cf logs "${CF_APP_NAME}" --recent
  '''
}
