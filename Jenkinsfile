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


        stage('Getting Started...'){
          steps {
             sh ' echo Hello, now will start your build and deployment.'
        }
    }

        stage('Starting Build and Deployment') {
          steps {
            sh '''
                 oc project $DEPLOY_PROJECT
                 oc -f cronjob.yaml create
               '''

          }
        } 

       
 
        stage('All Done'){
          steps {
             sh ' echo Your Pipeline is finished executing. Check the cronjob in $DEPLOY_PROJECT and jobs will be created from it.'
          }
       }

     }
} 
