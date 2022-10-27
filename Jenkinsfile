pipeline {
  
  agent any
  tools {
     jdk 'JAVA_HOME'
     maven 'M2_HOME'
  }
  environment {
        
        DOCKERHUB_CREDENTIALS = credentials('DockerHubID')
    }
  
  stages {
    stage ('Check Tools Initializing') {
            steps {
                sh 'java --version'
                sh 'mvn --version'
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
            }
        }
    stage('Cleaning the Project') {
      steps {
         sh 'echo "Clean the Project is processing ...."'
        sh 'mvn clean'
        
      }
      
    }
	  
   stage('Junit Testing') {
                              steps {
                               script {
                                sh 'echo "Test is processing ...."'
                                sh 'mvn clean test'
                               }

                              }

                            }

             stage('Quality Gate Status Check'){
                  steps{
                      script{
			      withSonarQubeEnv('Sonar') {
			      sh "mvn compile sonar:sonar"
                       	     	}
			      timeout(time: 1, unit: 'HOURS') {
			      def qg = waitForQualityGate()
				      if (qg.status != 'OK') {
					   error "Pipeline aborted due to quality gate failure: ${qg.status}"
				      }
                    		}
		    	    sh "mvn clean install"

                 	}
               	 }
              }	 
    
    stage('Junit Testing') {
      steps {
         sh 'echo "Junit Test is processing ...."'
        sh 'mvn  test'

      }
    }
    
    stage('Quality Gate Status Check'){
                  steps{
                      script{
			withSonarQubeEnv('sonar') {
			     sh "mvn compile sonar:sonar"
                       	          }
		    	    sh "mvn clean install"

                 	}
               	 }
              }
    
    stage ('Artifact construction') {
            steps {
                sh 'echo "Artifact construction is processing ...."'
                sh 'mvn  package' 
            }
      
            
        }
    
    
    
    stage('Docker build') {
    	agent any
      steps {
        sh 'echo "Docker is building ...."'
      	sh 'docker build -t $DOCKERHUB_CREDENTIALS_USR/devops_project_5ds1group5 .'
      }
  }
    
    
    stage('Docker login') {
    	agent any
      steps {
        sh 'echo "login Docker ...."'
      	sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
      }
  }
    
    stage('Docker push') {
    	agent any
      steps {
        sh 'echo "Docker is pushing ...."'
      	sh 'docker push $DOCKERHUB_CREDENTIALS_USR/devops_project_5ds1group5'
      }
  }
    
}
  post {
        success {
             mail to: "houaidafatma.karoui@esprit.tn",
                    subject: "Build sucess",
                    body: "sucess"
            echo 'successful'
        }
        failure {
             mail to: "houaidafatma.karoui@esprit.tn",
                    subject: "Build failed",
                    body: "failed"
            echo 'failed'
        }
      }
}



