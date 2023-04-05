pipeline {
  agent { label 'numericAgent' }
  parameters { 
    string(name: 'APP_NAME', defaultValue: '', description: 'What is the Heroku app name?') 
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t darinpope/java-web-app:latest .'
      }
    }
    
  }

}
