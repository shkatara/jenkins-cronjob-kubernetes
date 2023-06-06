pipeline {
    options {
        // set a timeout of 30 minutes for this pipeline
        timeout(time: 30, unit: 'MINUTES')
    }
    agent {
      node {
             label 'master'
      }
    }
    environment {
      DEPLOY_PROJECT = "jenkins-cron-deploy"
    }

    stages {
      stage('Starting Build and Deployment') {  
        steps {
          script {
            sh '''
               kubectl create deploy -n devops-tools httpd --image=docker.io/nginx:alpine 
               '''
        }    
      }
    } 
    stage('All Done'){
      steps {
        sh ' echo Your Pipeline is finished executing. Check the cronjob in $DEPLOY_PROJECT and jobs will be created from it.'
      }
    }
  
  }
} 
