pipeline{
    agent{ label "numericAgent"}

    options {
      buildDiscarder logRotator(artifactDaysToKeepStr: '1', artifactNumToKeepStr: '2', daysToKeepStr: '1', numToKeepStr: '2')
      timestamps()
     }

    environment {
              DEPLOY = "${env.BRANCH_NAME == "python-dramed" || env.BRANCH_NAME == "master" ? "true" : "false"}"
              NAME = "${env.BRANCH_NAME == "python-dramed" ? "example" : "example-staging"}"
              //def mvnHome =  tool name: "demo-maven:3.8.6", type: "maven"
              //def mavenCMD = "${mavenHome}/usr/share/maven"
              VERSION = "${env.BUILD_ID}"
              BUILD_NUMBER = "${env.BUILD_NUMBER}"
              BRANCH_NAME = "${env.BRANCH_NAME}"
              NODE_NAME = "${env.NODE_NAME}"
              REGISTRY = 'eagunuworld/drain-pepo'
              REGISTRY_CREDENTIAL = 'eagunuworld_dockerhub_creds'
            }

    stages {

      stage('cloning scm codes ') {
          steps {
            git branch: 'branch_walmart', credentialsId: 'democalculus_github_creds_ID', url: 'https://github.com/eagunuworld/JP_DranPope_App.git'
                   }
                }

      stage('Permission_build Jar codes') {
      steps {
        script {
          parallel(
            "Master": {
              sh "sudo chmod +rwx mvnw"
            },
            "Etcd": {
                sh './mvnw install'
            }
          )
        }
      }
    }


   }
}
