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
         input {
            message "Please enter the hostname"
            ok "Yes, enter the hostname"
            parameters {
              string(name: 'HOSTNAME_CATCH',defaultValue: '', description: '')
              string(name: 'PORT_CATCH',defaultValue: '', description: '')
            }
         }
         steps {
            // Do something with the inputs here\
            echo "Host is ${HOSTNAME_CATCH}"
            echo "Port is ${PORT_CATCH}"
              script {
                try {
                  sh '''
                    oc project $DEPLOY_PROJECT
                    oc get cm myconfig
                    oldHost=$(oc get cm myconfig -o yaml | yq e '.data.HOST' -)
                    oldPort=$(oc get cm myconfig -o yaml | yq e '.data.PORT' -)
                    echo old host is $oldHost
                    echo old port is $oldPost
                    echo new host is "${HOSTNAME_CATCH}"
                    echo new host is "${PORT_CATCH}"
                    oc get configmap myconfig -o yaml |  sed  "s/$oldHost/${HOSTNAME_CATCH}/g" | sed "s/$oldPort/${PORT_CATCH}/g" |oc replace -f -
                  '''
                }
                catch (Exception e) {
                    sh '''
                    oc project $DEPLOY_PROJECT
                    oc create configmap myconfig --from-literal=HOST=${HOSTNAME_CATCH} --from-literal=PORT=${PORT_CATCH}
                    '''
                }
              }
         }  
      }
      
    stage('Starting Build and Deployment') {
      input {
            message "Please enter the frequency"
            ok "Yes, enter the frequency"
            parameters {
              string(name: 'MIN_CATCH',defaultValue: '', description: '')
            }
      }  
      steps {
        script {
          try {
            sh '''
                  oc project $DEPLOY_PROJECT
                  oc get cronjob host-job
                  oc patch cronjob/host-job -p {"spec": {"schedule": "*/${MIN_CATCH} * * * *"}}
                '''
          }
          catch ( Exception e ) {
            sh '''
                  oc project $DEPLOY_PROJECT
                  oc create -f cronjob.yaml 
                  '''
          }
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
