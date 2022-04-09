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
        AWS_ECS_TASK_DEFINITION_PATH = 'simpletask.json'
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
          def login = ecrLogin()
          sh('#!/bin/sh -e\n' + "${login}") // hide logging
          docker.image("nginxdeploy:3").push()
        }
      }
    
  
}
        
        }

        stage('Deploy in ECS') {
        
         steps {
    
      script {
        updateContainerDefinitionJsonWithImageVersion()
        sh("/usr/local/bin/aws ecs register-task-definition --region ${AWS_ECR_REGION} --family ${AWS_ECS_TASK_DEFINITION} --container-definitions file://${AWS_ECS_TASK_DEFINITION_PATH}")
        def taskRevision = sh(script: "/usr/local/bin/aws ecs describe-task-definition --task-definition ${AWS_ECS_TASK_DEFINITION} | egrep \"revision\" | tr \"/\" \" \" | awk '{print \$2}' | sed 's/\"\$//'", returnStdout: true)
        sh("/usr/local/bin/aws ecs update-service --cluster ${AWS_ECS_CLUSTER} --service ${AWS_ECS_SERVICE} --task-definition ${AWS_ECS_TASK_DEFINITION}:${taskRevision}")
      }
    }
  }
        
        
    }
}
