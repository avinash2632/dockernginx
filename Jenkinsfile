pipeline {
    agent any


    environment {
     
        AWS_ECR_REGION = 'us-east-1'
        AWS_ECS_SERVICE = 'simpleservice'
        AWS_ECS_TASK_DEFINITION = 'nginxtask1'
        AWS_ECS_COMPATIBILITY = 'FARGATE'
        AWS_ECS_NETWORK_MODE = 'awsvpc'
        AWS_ECS_CPU = '256'
        AWS_ECS_MEMORY = '512'
        AWS_ECS_CLUSTER = 'nginxcluster'
        AWS_ECS_TASK_DEFINITION_PATH = 'taskdefinition.json'
        IMAGE_REPO = 'nginxdeploy'
        IMAGE_TAG = '3'
    }

    stages {
       
        
      
        stage('Build Docker Image') {
         steps {
  
      script {
        docker.build("nginxdeploy:3")
      }
    
  }
            
        }

        stage('Push Image to ECR') {
        
  steps {

      withAWS(region: "${AWS_ECR_REGION}", credentials: 'personal-aws-ecr') {
        script {
         
            sh "docker tag nginxdeploy:3 891437285629.dkr.ecr.us-east-1.amazonaws.com/jimbu:latest"
            sh "docker push 891437285629.dkr.ecr.us-east-1.amazonaws.com/jimbu:latest"
        }
      }
    
  }
}
         
        

        stage('Deploy in ECS') {
        
         steps {
    
      script {
        
         sh  "aws ecs register-task-definition --cli-input-json file://taskdefinition.json"
         sh "aws ecs create-service --cluster nginxcluster --service-name fargate-service --task-definition sample-fargate:1 --desired-count 1 --launch-type "FARGATE""
          
            
              
      }
    }
  }
        
        
    }
}
