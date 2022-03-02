
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

				//   sh "cp /var/lib/jenkins/.kube/config  /home/ubuntu/.kube/config"

				  sh 'kubectl get pods'
				   sh 'kubectl get nodes'
				  
			  }

			  }

		}

        stage('eks deploy') {

			steps {
				sh 'echo Hello World'
				sh 'kubectl get pods'
                // sh "sed -i 's/hellonodejs:latest/hellonodejs:eks/g' deploy.yaml"
                sh 'kubectl apply -f eks-deployment.yaml'
				sh 'kubectl get deployment'
                // sh 'kubectl rollout restart deployment hello-world-nodejs'
			}
		}
		stage('eks deploy') {

			steps {
                sh 'kubectl delete -f eks-deployment.yaml'
			}
		}
	}


}

