
pipeline{

	 environment {
    	registry = "flukyholy/devoptest"
    	registryCredential = 'dockerhub-flukky'
    	dockerImage = ''
		region = "ap-southeast-1"
		clusterName  = "flukky-lab"
  	}

	agent any
	
	stages {


		stage('Build') {
			steps {
			script {
         		 dockerImage = docker.build registry + ":$BUILD_NUMBER"
       		 }   
			}
		}
  

		stage('Push image') {
			steps {
				 script {
            		docker.withRegistry( '', registryCredential ) {
           			dockerImage.push()
         		 }
			}
		}

		}
        
		stage('Remove Unused docker image') {
     		 steps{
        		sh "docker rmi $registry:$BUILD_NUMBER"
     	 	}
   		}

		stage('aws creadentials'){
			steps {
			  withAWS(credentials: 'aws-flukky', region: 'ap-southeast-1') {
				  sh "aws iam list-account-aliases"
				  sh "aws eks --region $region update-kubeconfig --name $clusterName"
				  sh 'kubectl get pods'
				  sh 'kubectl get nodes'
				  
			  }
			}
		}

        stage('eks deploy') {

			steps {
				withAWS(credentials: 'aws-flukky', region: 'ap-southeast-1') {
				sh "aws eks --region $region update-kubeconfig --name $clusterName"
				sh 'echo Hello World'
				sh 'kubectl get pods'
                sh 'kubectl apply -f eks-deployment.yaml'
				sh 'kubectl get deployment'
				}
			}
		}
		stage('eks delete') {
			steps {
				withAWS(credentials: 'aws-flukky', region: 'ap-southeast-1') {
				sh "aws eks --region $region update-kubeconfig --name $clusterName"
                sh 'kubectl delete -f eks-deployment.yaml'
				}
			}
		}
	}


}

