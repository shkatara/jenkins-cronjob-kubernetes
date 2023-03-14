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
 
        stage('Prompt for Inputs'){
          steps {
            script {
              env.HOSTNAME = input message: 'Please enter the hostname to check for',parameters: [string(defaultValue: '',description: '', name: 'Username')]
            }
          }
        }    
      
        stage('Getting Started...'){
          steps {
             sh  "echo Hello, now will start your deployment and the hostname is ${env.HOSTNAME}"
          }
        }


        stage('Generating Configmap containing host ip / hostname'){
            try {
                sh '''
                oc project $DEPLOY_PROJECT
                oc get cm myconfig
                oldHost=$(oc get cm myconfig -o yaml | grep HOST  | cut -d':' -f 2)
                oc get configmap myconfig -o yaml |  sed  "s/$oldHost/${env.HOSTNAME}/g" | oc replace -f -
                '''
            }
            catch (exc) {
                sh '''
                oc project $DEPLOY_PROJECT
                oc create configmap myconfig --from-literal=HOST=${env.HOSTNAME}
                '''
            } 
        }
        stage('Starting Build and Deployment') {
          steps {
            sh '''
                 oc project $DEPLOY_PROJECT
                 oc get cronjob host-job
                 if [ $? != 0 ]
                 then
                   oc -f cronjob.yaml create
                 fi
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
