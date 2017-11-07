pipeline {
    agent any
    environment {
            // You need to specify 4 required environment variables first, they are going to be used for the following IBM Cloud DevOps steps
            IBM_CLOUD_DEVOPS_API_KEY = credentials('api_key_prod')
            IBM_CLOUD_DEVOPS_ORG = 'lix@us.ibm.com'
            IBM_CLOUD_DEVOPS_APP_NAME = 'test-slash'
            IBM_CLOUD_DEVOPS_TOOLCHAIN_ID = '3c93a66a-449c-4918-8795-7365c9197e3e'
        }
    tools {
        nodejs 'recent'
    }
    stages {
        
        stage('SCM') {
            steps {
                deleteDir()
                git 'https://github.com/xunrongl/DemoDRA-1.git'
            }
        }
        stage('Build') {
            environment {
                GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                GIT_BRANCH = 'master'
            }
            steps {
                sh 'npm --version'
               
            }
            // post build section to use "publishBuildRecord" method to publish build record
            post {
                success {
                    // post build section to use "publishBuildRecord" method to publish build record
                    publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "https://github.com/xunrongl/DemoDRA-1", result:"SUCCESS", buildNumber: "test/slash:1"
                }
                failure {
                	publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "https://github.com/xunrongl/DemoDRA-1", result:"FAIL", buildNumber: "test/slash:1"
                }
            }
        }
    }
}
