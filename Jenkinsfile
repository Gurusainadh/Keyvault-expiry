@Library("com.optum.jenkins.pipeline. library@master") _ import hudson.model.*
import groovy.json.JsonSlurper
pipeline {
    agent { node { label 'docker-maven-slave' } } 
    triggers {
      cron('0 0 * * 3')
    }
    options {
        buildDiscarder(
            logRotator(
                numToKeepStr: '4', // number of build logs to keep 
                daysToKeepStr: '10', // history to keep in days
            }
        }
    }
              
    stages {
        stage('Create SP List'){
            steps {
                script {
                    list = [
                        "AZU_OSFEngine_NonProd": [
                        1,
                        "id": "a2baf1b3-2028-483d-blab-a4e947844587", //"sp": "coe-osfe-nonprod-sp"
                        "AZU_OSFEngine_Prod": [
                        1,
                        "id": "39179f5b-0965-4395-b484-31011888e0a8", "sp": "coe-osfe-prod-sp"
                        "AZU_RXCLOUDEngine_Non_Prod": [
                        1,
                        "id":"600f90f7-137c-43f4-9a82-5ad7352fe979",
                        "sp": "coe-rxcloudengine-non-prod-sp"
                        "AZU_RXCLOUDEngine_Prod": [
                        "id": "e150e01f-9d8c-44c5-a5b0-749d1fc17c52", "sp": "coe-rxcloudengine-prod-sp"
                        ],
                        "RxCloudESB NonProd": [
                              "id": "ab91f2a7-3fa2-4c95-bba5-aed4019405ea", 
                              "sp": "coe-RxCloudESB-non-prod-sp"
                         ]
                    ]
                }
            }
        }
        stage('Run Audit') {
            steps {
                script {
                    list.each{
                        stage(it.key){
                            environment {
                                subId=it.value.id
                                subName=it.key
                            }
                            script {
                                withCredentials([azureServicePrincipal(it.value.sp)]) {
                                  sh '''
                                  . /etc/profile.d/jenkins.sh
                                  set +x
                                  az login --service-principal -u $AZURE_CLIENT_ID -p=$AZURE_CLIENT_SECRET - SAZURE TENANT_ID access_token=$(az account get-access-token)
                                  export AZURE_EXTENSION_DIR="${WORKSPACE}/.azure/mixins"
                                  az version
                                  mkdir -p $AZURE_EXTENSION_DIR
                                  az extension add --name resource-graph
                                  az extension list
                                  pip install --upgrade pip && pip install pandas
                                  bash keyvault-expiry.shit.value.id++it.key+'''"
                                  bash keyvault-single-report.shit.value.id++it.key+'''"
                                  '''
                                  sh "set -x"                  
                                } 
                            }
                        }
                    }
                }
            }
        }
    }
    post {
      success{
          archiveArtifacts artifacts: '*.csv', onlyIfSuccessful: true
          emailext attachLog: false, attachmentsPattern: *.csv',
          body: "Please find attached: Azure Keyvualt Certs Expiry Report Status",
          recipientProviders: [requestor()],
          subject: "Azure KeyVault Cert Expiry Report",
          to: "params.recipient, DevOps_CE@ds.com"
      }  
      failure {
          emailext attachLog: true,
          body: "The build has failed has failed for Azure Keyvault Certs Expiry Reporting. Please check the attached log for details.", recipientProviders: [requestor()],
          subject: "Build Failure Notification",
          to: "params.recipient, osman.com, "
      }
  }                          
}    
                          
                          

                          




                          
