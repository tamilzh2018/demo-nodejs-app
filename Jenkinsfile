pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="711327469867"
        AWS_DEFAULT_REGION="ap-south-1" 
	CLUSTER_NAME="ecs-demo"
	SERVICE_NAME="ecs-service-demo"
	TASK_DEFINITION_NAME="ecs-task-demo"
	DESIRED_COUNT="1"
        IMAGE_REPO_NAME="image-repo"
        IMAGE_TAG="${env.BUILD_ID}"
        //REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
	REPOSITORY_URI = "http://192.168.43.89:8081/repository/image-repo/"
	registryCredential = "nexus-admin"
	registryCredentialAws = "aws-cred"
    }
   
    stages {

    // Tests
    stage('Unit Tests') {
      steps{
        script {
          sh 'npm install'
	  sh 'npm test -- --watchAll=false'
        }
      }
    }
        
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to Nexus') {
     steps{  
         script {
			docker.withRegistry("REPOSITORY_URI" + registryCredential) {
                    	dockerImage.push()
                	}
         }
        }
      }
      
    stage('Deploy') {
     steps{
            withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh 'chmod +x script.sh '
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}
