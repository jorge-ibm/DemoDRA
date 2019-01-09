#!groovy
/*
    Sample weather app that deploys to cloud foundry
 */

pipeline {
    agent any
    tools {
      nodejs "recent"
    }
    environment {
        // You need to specify 4 required environment variables first, they are going to be used for the following IBM Cloud DevOps steps
        IBM_CLOUD_DEVOPS_API_KEY = credentials('sample-apikey-weather-app')
        IBM_CLOUD_DEVOPS_APP_NAME = 'Weather-App-Demo-Sample'
    }
    parameters {
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
          deployStaging(params.organization)
        }
      }

      stage('Deploy to Prod') {
        steps {
          deployProd(params.organization)
        }
      }
    }
}
def buildImage() {
  sh '''
  #!/bin/bash +x
  echo -e "Building container image"
  npm --version
  npm install
  grunt dev-setup --no-color
  '''
}
def unitTest() {
  sh '''
  #!/bin/bash

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
def deployStaging(organization) {
  sh '''
  #!/bin/bash
  # Push app
  export CF_APP_NAME="staging-${IBM_CLOUD_DEVOPS_APP_NAME}"

  echo "Installing cloud foundry"
  wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | sudo apt-key add -
  echo "deb https://packages.cloudfoundry.org/debian stable main" | sudo tee /etc/apt/sources.list.d/cloudfoundry-cli.list
  sudo apt-get update
  sudo apt-get install cf-cli
  echo "Done"

  cf api https://api.ng.bluemix.net
  cf login -u apikey -p $IBM_CLOUD_DEVOPS_API_KEY -o $organization
  cf push "${CF_APP_NAME}"
  # View logs
  #cf logs "${CF_APP_NAME}" --recent
  '''
}
def deployProd(organization) {
  sh '''
  #!/bin/bash
  # Push app
  export CF_APP_NAME="${IBM_CLOUD_DEVOPS_APP_NAME}"
  cf push "${CF_APP_NAME}"
  export APP_URL=http://$(cf app $CF_APP_NAME | grep -e urls: -e routes: | awk '{print $2}')
  # View logs
  #cf logs "${CF_APP_NAME}" --recent
  '''
}
