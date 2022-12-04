def branch_exist = false
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
                                    branch_exist = true
                                    echo ' inside error trap'
                                  
                                    echo "'${branch_exist}'"
                                   }
                
            }
                  
            }
        }
        
        stage('perform CI for existing branch') {
            
                 when {
                    allOf{
                        expression{ branch_exist == true}
                        triggeredBy cause: 'UserIdCause'
                    }
                        
                        beforeAgent true
                }

                    
                stages {
                    
                       
                                stage('Build') { 
                                            steps { 
                                                script{
                                                    app = docker.build("noah-jenkins-ecr-repo", ".")
                                                
                                                    }
                                                }
                                }
                                
                                stage('Test'){
                                    steps {
                                    script{
                                            
                                            sh 'docker run -d -p8083:8080 --name cowsaytest noah-jenkins-ecr-repo'
                                            sh 'sleep 2'
                                            sh "curl http://ec2-34-222-28-140.us-west-2.compute.amazonaws.com:8083"
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
        stage('create new Release branch') {
            when {
                    allOf{
                        expression{ branch_exist == false}
                        triggeredBy cause: 'UserIdCause'
                    }
                        
                        beforeAgent true
                }
                steps{
                    script{
                       sh """
                        git config --global user.email "abekah.ekow@gmail.com"
                        git config --global user.name "the developer ekow"
                        git checkout origin/main
                        git checkout -b release/${params.VERSION_BRANCH}
                        git commit -am "new release branch ${params.VERSION_BRANCH}"
                        git push --set-upstream origin release/${params.VERSION_BRANCH}
                        echo "${params.VERSION_BRANCH}/n NOT FOR RELEASE" > v.txt
                        """
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
                                    
                                                script{
                                                sh 'docker rm -f cowsaytest'
                                                sh 'docker image rm -f noah-jenkins-ecr-repo'
                                                }
                                                emailext attachLog: true, body: "Cowsay build ${currentBuild.currentResult}", compressLog: true, recipientProviders: [culprits(), previous()], subject: "Build ${currentBuild.currentResult}", to: 'abekah.ekow@gmail.com'
                                    
                                                echo 'noah'
                                            }
                        
    } 
}