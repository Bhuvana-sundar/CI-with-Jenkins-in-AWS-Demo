pipeline {
        agent any
        environment {
            registry = "bhuvanadevopsengineer/sprint6"
            registryCredential = 'newdockerkey'
            dockerImage = ''
		PROJECT_ID = 'engaged-droplet-285511'
 		CLUSTER_NAME = 'gcloud-k8s-jenkins-cluster'
 		LOCATION = 'europe-west2-a'
 		CREDENTIALS_ID = 'jenkins-k8'
        }
		
	    stages {	
		   stage('Scm Checkout') {            
			steps {
	                  checkout scm
			}	
	           }
	           
		   stage('Build') { 
	                steps {
	                  echo "Cleaning and packaging..."
	                  sh 'mvn clean package'		
	                }
	           }
		   stage('Test') { 
			steps {
		          echo "Testing..."
			  sh 'mvn test'
			}
		   }
		   stage('Build Docker Image') { 
			steps {
	                   script {
	                      dockerImage = docker.build registry + ":$BUILD_NUMBER"
	                   }
	                }
		   }
            stage('Deploy Image') {
                steps{
                    script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                    }
                }
            }
	           stage('Deploy to GKE') {
 			steps{
 				echo "Deployment started"
				sh 'ls -ltr'
				sh 'pwd'
				sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID,
				      clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml',
				      credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				echo "Deployment Finished"
 	            }
	          }
	    }
	}
