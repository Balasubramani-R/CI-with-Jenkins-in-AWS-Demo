pipeline {
    agent any 	
	environment {
		
		PROJECT_ID = 'devopsbalu'
                CLUSTER_NAME = 'kubern8'
                LOCATION = 'us-central1-c'
                CREDENTIALS_ID = 'kubernetes'
		
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
                    myapp1 = docker.build("brsmnb/kube9s:${env.BUILD_ID}")
		    //myapp = docker.build("gcr.io/devopsbalu/brsmnb/kube8s:${env.BUILD_ID}")
                   }
                }
	   }
	   stage("Push Docker Image") {
                steps {
                   script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
                    //docker.withRegistry('https://gcr.io') {
			    myapp1.push("${env.BUILD_ID}")		
                     }
                   }
                }
            }
	   
           stage('Deploy to K8s') { 
                steps{
                   echo "Deployment started ..."
		   sh 'ls -ltr'
		   sh 'pwd'
		   sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
		   step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
		   echo "Deployment Finished ..."
            }
          }
    }
}
