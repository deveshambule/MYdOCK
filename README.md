# MYdOCK


Pipeline Techtrail

Pravinyam Access Details:

Hello Varsha,


Please find below your Pravinyam access details:


Link: https://pravinyam.co.in/


User name: varsha.patil@tech-trail.com


Password: gyi1f751zyAVHCWLAQ48K


Feel free to change password after first login.


Regards,
Javed








========================================================================
PIPLINE
========================================================================

pipeline {
   agent any
   environment {
       dockerImage =''
       registry = 'devesh/xuriti_frountend2'

       
       }
   
   stages {
    stage ('PULL'){
        steps {
          git branch: 'development', credentialsId: 'ebd1f3ed-999a-4a43-8b2d-2bd35c0655b8', url: 'https://github.com/Deveshkumarambule/Local-Host.git'
           sh 'ls'
    } 
    }
          stage('Build') { 
        steps {
            sh '''
           
            cd user_panel
            rm -rf dist
            rm -rf package-lock.json
           
            npm cache clean --force
            
           npm install --force --save
            
        
            npm  run build -prod
           
             
             

            cd ../admin_panel
            rm -rf dist
            rm -rf package-lock.json
           
            npm cache clean --force
          
            
            npm install --force --save
            
    
            npm  run build -prod
             
             

            cd ../
        
            '''
            }
        }
        
        stage ('docker build') {
            steps {
                script {
                    dockerImage = docker.build registry 
                }
            }
        }
        stage('run_docker_image'){
            steps
        {
            sh 'sudo docker run -d -p 8084:80 devesh/xuriti_frountend2'

        }
        }
        

     
    }
}















==================================================================
       				Back End Pipline
==================================================================


pipeline {
 agent any
 stages {
       stage("git_pull"){
         steps {
         git branch: 'main', credentialsId: 'devesh', url: 'https://github.com/Deveshkumarambule/jenkins-backend-local.git'
         }
     } 
     
 stage("verify tooling") {
 steps {
 sh '''
 docker version
 docker info
  
 
 '''
 }
     
 }
 stage('scm_checkout'){
     steps{
          sh 'docker-compose stop'  
    
     }
 }
 stage('Prune Docker data') {
 steps {
 sh 'docker system prune -a --volumes -f'
 }
 }
 stage('Start container') {

      steps {
     
          sh 'docker compose build'
          sh 'docker compose up'

           }


 }
 
}
}



==================================================================
           	FrontEnd flush In FlushOut Pipline
==================================================================

pipeline {
 agent any
    stages {
        
     stage('Backup MongoDB') {
        steps {
          script {
            def dbName = 'xuritidb'   
            def backupDir = '/home/devesh/'
              // Restore MongoDB database
          sh "sudo mongodump --db $dbName --out $backupDir"
           echo "all steps done "
          }
      }
    }
     stage('print'){
         steps{
             echo 'hi everyone'
         }
     }
     
     stage('MongoDB Cleanup') {
            steps {
              script {
                          // Define MongoDB connection parameters
               def mongodbHost = '192.168.0.128'
               def mongodbPort = '27017'
               def mongodbUsername = ''
               def mongodbPassword = ''
               def databaseName = 'xuritidb'

              // Set up MongoDB connection string
               def connectionString = "mongodb://${mongodbUsername}:${mongodbPassword}@${mongodbHost}:${mongodbPort}/${databaseName}"

              // Execute MongoDB shell commands
               def mongoShellCommands = """
              // MongoDB shell commands here

              use ${databaseName}

              db.access.configs.drop()
              db.error_logs.drop()
              db.agent_forms.drop()
              db.agent_leads.drop()
              db.agent_pds.drop()
              db.api_logs.drop()
              db.audits.drop()
              db.banks.drop()
              db.bankstatement-reports.drop()
              db.cash_in_hands.drop()
              db.chat_dumps.drop()
              db.client_keys.drop()
              db.collections.drop()
              db.credit_company_rels.drop()
              db.credit_histories.drop()
              db.discount_invoice_rels.drop()
              db.discounts.drop()
              db.documents.drop()
              db.employeedatas.drop()
              db.entity_cpv_archives.drop()
              db.entity_cpvs.drop()
              db.entity_details.drop()
              db.entity_gst_details.drop()
              db.entity_interests.drop()
              db.entityevaluationrequests.drop()
              db.error_logs.drop()
              db.event_logs.drop()
              db.extend_credits.drop()
              db.invoice_interests.drop()
              db.invoices.drop()
              db.kyc_esigns.drop()
              db.kycs.drop()
              db.ledgers.drop()
              db.nbfc_files.drop()
              db.nbfc_maapings.drop()
              db..nbfc_mappings.drop()
              db.nbfc_maps.drop()
              db.nbfc_payments.drop()
              db.nbfcmodels.drop()
              db.nbfcreconcelations.drop()
              db.otps.drop()
              db.paymentlinks.drop()
              db.payments.drop()
              db.rabbit_logs.drop()
              db.response_chats.drop()
              db.sent_chats.drop()
              db.sms_delivery_reports.drop()
              db.transactions.drop()
              db.user_datas.drop()
              db.user_ips.drop()
              db.wavers.drop()
              db.transactions.drop()
              db.nbfc_mappings.drop()
              db.bankstatement-reports.drop()
              db.disbursements.drop()
               db.entities.drop()
               db.entity_relations.drop()
               db.users.drop()
              
              
                                   """

                     // Execute MongoDB commands
                  sh(script: """
                   echo '${mongoShellCommands}' | mongo ${connectionString}
   
   
   
                     """)
           }
      }
 }
 stage('Localdatabase') {
        steps {
          script {
            def dbName = 'xuritidb'   
            def backupDir = '/home/devesh/LocalDB/'
              // Restore MongoDB database
          sh "sudo mongorestore --db $dbName $backupDir/$dbName"
           echo "all steps done "
          }
      }
    }
 stage('trigger_test_case'){
         steps{
             build job: 'UI_Posstive_Automation', waitForStart: true
             echo 'after_completed'
         }
     }
    
  stage('trigger_test_cases'){
         steps{
             build job: 'UI_Negative_Automation', waitForStart: true
             echo 'after_completed'
         }
     }
 

}
}
   
     
   

==================================================================
           	BackEnd flush In FlushOut Pipline(For API)
==================================================================
pipeline{
    agent any
stages{    

     stage('after_copition'){
         steps{
             echo 'completed ui_test'
         }
     }
     stage('Localdatabase1') {
        steps {
          script {
            def dbName = 'xuritidb'   
            def backupDir = '/home/serverpc/Downloads/LocalDB'
              // Restore MongoDB database
          sh "sudo mongorestore --db $dbName $backupDir/$dbName"
           echo "all steps done "
          }
      }
    }

     stage('MongoDB2 Cleanup') {
            steps {
              script {
                          // Define MongoDB connection parameters
               def mongodbHost = '192.168.0.128'
               def mongodbPort = '27017'
               def mongodbUsername = ''
               def mongodbPassword = ''
               def databaseName = 'xuritidb'

              // Set up MongoDB connection string
               def connectionString = "mongodb://${mongodbUsername}:${mongodbPassword}@${mongodbHost}:${mongodbPort}/${databaseName}"

              // Execute MongoDB shell commands
               def mongoShellCommands = """
              // MongoDB shell commands here

              use ${databaseName}

              db.access.configs.drop()
              db.error_logs.drop()
              db.agent_forms.drop()
              db.agent_leads.drop()
              db.agent_pds.drop()
              db.api_logs.drop()
              db.audits.drop()
              db.banks.drop()
              db.bankstatement-reports.drop()
              db.cash_in_hands.drop()
              db.chat_dumps.drop()
              db.client_keys.drop()
              db.collections.drop()
              db.credit_company_rels.drop()
              db.credit_histories.drop()
              db.discount_invoice_rels.drop()
              db.discounts.drop()
              db.documents.drop()
              db.employeedatas.drop()
              db.entity_cpv_archives.drop()
              db.entity_cpvs.drop()
              db.entity_details.drop()
              db.entity_gst_details.drop()
              db.entity_interests.drop()
              db.entityevaluationrequests.drop()
              db.error_logs.drop()
              db.event_logs.drop()
              db.extend_credits.drop()
              db.invoice_interests.drop()
              db.invoices.drop()
              db.kyc_esigns.drop()
              db.kycs.drop()
              db.ledgers.drop()
              db.nbfc_files.drop()
              db.nbfc_maapings.drop()
              db..nbfc_mappings.drop()
              db.nbfc_maps.drop()
              db.nbfc_payments.drop()
              db.nbfcmodels.drop()
              db.nbfcreconcelations.drop()
              db.otps.drop()
              db.paymentlinks.drop()
              db.payments.drop()
              db.rabbit_logs.drop()
              db.response_chats.drop()
              db.sent_chats.drop()
              db.sms_delivery_reports.drop()
              db.transactions.drop()
              db.user_datas.drop()
              db.user_ips.drop()
              db.wavers.drop()
              db.transactions.drop()
              db.nbfc_mappings.drop()
              db.bankstatement-reports.drop()
              db.disbursements.drop()
              db.entities.drop()
               db.entity_relations.drop()
               db.users.drop()

                """

                     // Execute MongoDB commands
                  sh(script: """
                   echo '${mongoShellCommands}' | mongo ${connectionString}
   
   
   
                     """)
           }
      }
 }
      stage('Localdatabase11') {
        steps {
          script {
            def dbName = 'xuritidb'   
            def backupDir = '/home/serverpc/Downloads/LocalDB'
              // Restore MongoDB database
          sh "sudo mongorestore --db $dbName $backupDir/$dbName"
           echo "all steps done "
          }
      }
    }
    stage('trigger_test_case'){
         steps{
             build job: 'API_automation', waitForStart: true
             echo 'after_completed'
         }
     }
        }    
}






===================================================================

pipeline {
 agent any
    stages{
     
     stage('print'){
         steps{
             echo 'hi everyone'
         }
     }
     
     stage('MongoDB Cleanup') {
            steps {
              script {
                          // Define MongoDB connection parameters
              def mongodbHost = '192.168.0.117'
              def mongodbPort = '27017'
              def mongodbUsername = 'xuritiDB'
              def mongodbPassword = 'xuriti%40xuritiDB%40123%40098'
              def databaseName = 'xuritidb'

              // Set up MongoDB connection string
              def connectionString = "mongodb://${mongodbUsername}:${mongodbPassword}@${mongodbHost}:${mongodbPort}/${databaseName}"

              // Execute MongoDB shell commands
              def mongoShellCommands = """
              // MongoDB shell commands here

              use ${databaseName}

              db.access.configs.drop()
              db.error_logs.drop()
              db.agent_forms.drop()
              db.agent_leads.drop()
              db.agent_pds.drop()
              db.api_logs.drop()
              db.audits.drop()
              db.banks.drop()
              db.cash_in_hands.drop()
              db.chat_dumps.drop()
              db.client_keys.drop()
              db.collections.drop()
              db.credit_company_rels.drop()
              db.credit_histories.drop()
              db.discount_invoice_rels.drop()
              db.discounts.drop()
              db.documents.drop()
              db.employeedatas.drop()
              db.entity_cpv_archives.drop()
              db.entity_cpvs.drop()
              db.entity_details.drop()
              db.entity_gst_details.drop()
              db.entity_interests.drop()
              db.entityevaluationrequests.drop()
              db.error_logs.drop()
              db.event_logs.drop()
              db.extend_credits.drop()
              db.invoice_interests.drop()
              db.invoices.drop()
              db.kyc_esigns.drop()
              db.kycs.drop()
              db.ledgers.drop()
              db.nbfc_files.drop()
              db.nbfc_maapings.drop()
              db.nbfc_mappings.drop()
              db.nbfc_maps.drop()
              db.nbfc_payments.drop()
              db.nbfcmodels.drop()
              db.nbfcreconcelations.drop()
              db.otps.drop()
              db.paymentlinks.drop()
              db.payments.drop()
              db.rabbit_logs.drop()
              db.response_chats.drop()
              db.sent_chats.drop()
              db.sms_delivery_reports.drop()
              db.transactions.drop()
              db.user_datas.drop()
              db.user_ips.drop()
              db.wavers.drop()
              db.transactions.drop()
              db.nbfc_mappings.drop()
              db.bankstatement-reports.drop()
              db.disbursements.drop()
              db.entities.drop()
              db.entity_relations.drop()
              db.users.drop()
              
                                  """

                     // Execute MongoDB commands
                  sh(script: """
                  echo '${mongoShellCommands}' | mongosh ${connectionString}
                 
                     """)
                     
                 
          }
      }
 }
        stage('MongoDB restore2') {
            steps {
              script {
                          // Define MongoDB connection parameters
            //  def mongodbHost = '192.168.0.117'
          //   def mongodbPort = '27017'
            //  def mongodbUsername = 'xuritiDB'
          //  def mongodbPassword = 'xuriti%40xuritiDB%40123%40098'
            //  def databaseName = 'xuritidb'
               
              // Define the database name and backup folder
                    def dbName = 'xuritidb'
                    def backupFolder = '/home/serverpc/Documents/LocalDB/'

                    // Run mongorestore with authentication

              // Set up MongoDB connection string
              //def connectionString = "mongodb://${mongodbUsername}:${mongodbPassword}@${mongodbHost}:${mongodbPort}/${databaseName}"

              // sh "sudo mongorestore --db $backupFolder/$dbName"
              

        sh " sudo mongorestore --host=192.168.0.117 --port=27017 --username=xuritiDB --password 'xuriti@xuritiDB@123@098' --authenticationDatabase=xuritidb /home/serverpc/Documents/LocalDB/"


              // Execute MongoDB shell commands
              def mongoShellCommands = """
               
               
              """
              echo 'Printed Successfully'
              
              sh 'exit 0'
            }
 }
            }
      stage('trigger_test_case'){
         steps{
              
             build job: 'UI_Negative_Automation', waitForStart: true
             echo 'after_completed'
              }
         
     }
     
     stage('trigger_test_case'){
         steps{
             build job: 'UI_Posstive_Automation', waitForStart: true
             echo 'after_completed'
         }
     

 }
    }
}


===================================================DataBase Cred. Pipeline PlushIn-Plushout
===================================================

pipeline {
 agent any
    stages {
     
     stage('print'){
         steps{
             echo 'hi everyone'
         }
     }
     
     stage('MongoDB Cleanup') {
            steps {
              script {
                          // Define MongoDB connection parameters
              def mongodbHost = '192.168.0.117'
              def mongodbPort = '27017'
              def mongodbUsername = 'xuritiDB'
              def mongodbPassword = 'xuriti%40xuritiDB%40123%40098'
              def databaseName = 'xuritidb'

              // Set up MongoDB connection string
              def connectionString = "mongodb://${mongodbUsername}:${mongodbPassword}@${mongodbHost}:${mongodbPort}/${databaseName}"

              // Execute MongoDB shell commands
              def mongoShellCommands = """
              // MongoDB shell commands here

              use ${databaseName}

              db.access.configs.drop()
              db.error_logs.drop()
              db.agent_forms.drop()
              db.agent_leads.drop()
              db.agent_pds.drop()
              db.api_logs.drop()
              db.audits.drop()
              db.banks.drop()
              db.cash_in_hands.drop()
              db.chat_dumps.drop()
              db.client_keys.drop()
              db.collections.drop()
              db.credit_company_rels.drop()
              db.credit_histories.drop()
              db.discount_invoice_rels.drop()
              db.discounts.drop()
              db.documents.drop()
              db.employeedatas.drop()
              db.entity_cpv_archives.drop()
              db.entity_cpvs.drop()
              db.entity_details.drop()
              db.entity_gst_details.drop()
              db.entity_interests.drop()
              db.entityevaluationrequests.drop()
              db.error_logs.drop()
              db.event_logs.drop()
              db.extend_credits.drop()
              db.invoice_interests.drop()
              db.invoices.drop()
              db.kyc_esigns.drop()
              db.kycs.drop()
              db.ledgers.drop()
              db.nbfc_files.drop()
              db.nbfc_maapings.drop()
              db.nbfc_mappings.drop()
              db.nbfc_maps.drop()
              db.nbfc_payments.drop()
              db.nbfcmodels.drop()
              db.nbfcreconcelations.drop()
              db.otps.drop()
              db.paymentlinks.drop()
              db.payments.drop()
              db.rabbit_logs.drop()
              db.response_chats.drop()
              db.sent_chats.drop()
              db.sms_delivery_reports.drop()
              db.transactions.drop()
              db.user_datas.drop()
              db.user_ips.drop()
              db.wavers.drop()
              db.transactions.drop()
              db.nbfc_mappings.drop()
              db.bankstatement-reports.drop()
              db.disbursements.drop()
              db.entities.drop()
              db.entity_relations.drop()
              db.users.drop()
              
                                  """

                     // Execute MongoDB commands
                  sh(script: """
                  echo '${mongoShellCommands}' | mongosh ${connectionString}
                 
                     """)
          }
      }
 }
        stage('MongoDB restore2') {
            steps {
              script {
             
                    def dbName = 'xuritidb'
                    def backupFolder = '/home/serverpc/Documents/LocalDB/'

         
           
              

     sh " sudo mongorestore --host=192.168.0.117 --port=27017 --username=xuritiDB --password 'xuriti@xuritiDB@123@098' --authenticationDatabase=xuritidb /home/serverpc/Documents/LocalDB/"

              // Execute MongoDB shell commands
              def mongoShellCommands = """
               
               
              """
              echo 'Printed Successfully'
            }
 }
            }
         
 }
 

  

    }


