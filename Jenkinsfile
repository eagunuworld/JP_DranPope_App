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
               CI = true
              JFROG_TOKEN = credentials('jfrog_artifactory_access_west_north_tokenID') 
            }

    stages {

      stage('cloning scm codes ') {
          steps {
            git branch: 'branch_walmart', credentialsId: 'democalculus_github_creds_ID', url: 'https://github.com/eagunuworld/JP_DranPope_App.git'
                   }
                }

      stage('Files and Permissions') {
      steps {
        script {
          parallel(
            "mvnw permission": {
              sh "sudo chmod +rwx mvnw"
            },
            "Displaying files": {
                sh 'ls -lart'
            }
          )

        }
      }
    }

  stage('Build Jar codes') {
      steps {
        script {
          parallel(
            "working": {
              sh "printenv"
            },
            "build jar packages": {
                sh './mvnw install'
            }
          )

        }
      }
    }

  stage('Upload Binaries to Jfrog Artifactory') {
        steps {
        sh 'jf rt upload --url http://34.174.20.73:8082/artifactory/ --access-token ${JFROG_TOKEN} target/demo-0.0.1-SNAPSHOT.jar oi_java_web_app/'
          }
        }
  
  stage('Building Docker Image'){
          steps{
              sh '''
              docker build -t localhost:8082/java-web-app-docker/demoapp:$BUILD_NUMBER --pull=true .
              docker images
              '''
          }
      }

  stage('Image Scanning Trivy'){
            steps{
               sh 'trivy image localhost:8082/java-web-app-docker/demoapp:$BUILD_NUMBER > $WORKSPACE/trivy-image-scan-$BUILD_NUMBER.txt'
            }
        }

  stage('Uploading Image Scan to Jrog Artifactory'){
         steps{
          sh 'jf rt upload --url http://34.174.20.73:8082/artifactory/ --access-token  ${JFROG_TOKEN} trivy-image-scan-$BUILD_NUMBER.txt oi_java_web_app/'           
         }
     }

  stage('RemoveResources') {  
      steps {
         parallel(
               "KillProcesses": {
                    sh "docker rmi localhost:8082/java-web-app-docker/demoapp:$BUILD_NUMBER" 
                  },
                 "RemoveDockerImages": {
                  sh 'docker rmi  -f $(docker images -q)'
                }
             )
         }
      }

   }
}
