def branch_exist = "false"
pipeline {
    
    parameters {
      string name: 'VERSION_BRANCH', trim: true

  
    }

   

    agent any

    stages {
        stage ('prompt for input') {
            steps{
            
                                catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                                
                                    echo "$params.VERSION_BRANCH"
                                    git branch: "${params.VERSION_BRANCH}" , credentialsId: 'feefb181-a57e-4003-9f28-9487d93d1122', url: 'http://34.222.28.140:8086/ekow_developer/cowsay-versioned.git'    
                                    
                                    script {
                                    branch_exist = "true"
                                    echo ' inside error trap'
                                  
                                    echo "'${branch_exist}'"
                                   }
                
            }
                  
            }
        }
        
        stage('branch_exist') {
            
            when {
                
                 
                    allof {
                        branch_exist='true' 
                        triggeredBy cause: 'UserIdCause'
                    }
                    beforeAgent true
                 }

                    
                stages {
                    
                       
                                stage('Build') { 
                                            steps { 
                                                script{
                                                    app = docker.build("noah-jenkins-ecr-repo", "./src")
                                                
                                                    }
                                                }
                                }
                                
                                stage('Test'){
                                    steps {
                                    script{
                                            sh "docker -d p'8087:8082' --name cowsaytest ${}:$tag"
                                            sh "sleep 8"
                                            sh "curl http://ec-unbutu-ec2-larg:8087"
                                            }
                                    }
                                }

                                stage('Deploy to Ecr') {
                                    steps {
                                        script{
                                            
                                            docker.withRegistry('https://644435390668.dkr.ecr.us-west-2.amazonaws.com/noah-jenkins-ecr-repo', 'ecr:us-west-2:69e49cf8-529f-45b4-bb52-cee6a094adca') {
                                                app.push("${env.BUILD_NUMBER}")
                                            }
                                        }
                                    }
                                }   

                                
                    

                    
                
                }
       
    
    
    }

    post {

                                            success {
                                    
                                                updateGitlabCommitStatus name: 'build', state: 'success'
                                    
                                            }
                                    
                                            failure {
                                    
                                                updateGitlabCommitStatus name: 'build', state: 'failed'
                                    
                                            }
                                    
                                            always {
                                    
                                                emailext attachLog: true, body: "Cowsay build ${currentBuild.currentResult}", compressLog: true, recipientProviders: [culprits(), previous()], subject: "Build ${currentBuild.currentResult}", to: 'immanuelcromwell@gmail.com'
                                    
                                                echo 'Cle'
                                            }
                        
    } 
}