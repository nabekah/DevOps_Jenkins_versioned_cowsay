pipeline {
    
   parameters {
  string name: 'VERSION_BRANCH', trim: true
}

    agent any
    stages {
        stage ('prompt for input') {
            steps{
            timeout(time: 120, unit: 'SECONDS'){
                waitUntil {
                   script {
                        sh """
                            try {
                                
                                    echo "$params.VERSION_BRANCH"
                                    git branch: "${params.VERSION_BRANCH}" , credentialsId: 'feefb181-a57e-4003-9f28-9487d93d1122', url: 'http://34.222.28.140:8086/ekow_developer/cowsay-versioned.git'    
                                
                            
                                
                                return true   
                            }
                            catch (execption){
                                return false
                            }
                            """
                   }
                
                }
                 
            }
                  
            }
        }
        
        stage('GitLabWebHookCause') {
            steps {
                echo "I am only executed when triggered by SCM push"
            }

            when {
                  beforeAgent true
                 triggeredBy 'GitLabWebHookCause'
            }
        }
  }
    
}