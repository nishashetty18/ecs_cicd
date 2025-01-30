pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'  // Set your AWS region
        ECR_REPOSITORY = 'sample'  
        ECS_CLUSTER = 'new-app'  
        ECS_SERVICE = 'demoservice'  
        TASK_DEFINITION_FILE = 'task-defination.json' 
        IMAGE_TAG = "${env.BUILD_NUMBER}" 
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    checkout scm
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def image = "686255964186.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}"
                    withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws_key', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin 686255964186.dkr.ecr.${AWS_REGION}.amazonaws.com
                        docker build -t ${image} .
                        docker push ${image}
                    """
                    }
                    sh """
                        jq '.containerDefinitions[0].image = "${image}"' ${TASK_DEFINITION_FILE} > updated-task-definition.json
                    """
                    }
                }
            }
        }
        stage('Register ECS Task Definition') {
            steps {
                script {
                    withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws_key', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh """
                        aws ecs register-task-definition --cli-input-json file://updated-task-definition.json
                    """
                    }
                }
            }
        }

        stage('Update ECS Service') {
            steps {
                script {
                    withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws_key', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh """
                        aws ecs update-service --cluster ${ECS_CLUSTER} --service ${ECS_SERVICE} --force-new-deployment
                    """
                }
            }
        }   
    }

