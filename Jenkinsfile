pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'  // Set your AWS region
        your-account-id = '686255964186'
        ECR_REPOSITORY = 'sample'  
        ECS_CLUSTER = 'new-app'  
        ECS_SERVICE = 'demoservice'  
        TASK_DEFINITION_FILE = 'new-app' 
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
                    def image = "${your-account-id}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}"
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin your-account-id.dkr.ecr.${AWS_REGION}.amazonaws.com
                        docker build -t ${image} .
                        docker push ${image}
                    """
                }
            }
        }

        
    }
}
