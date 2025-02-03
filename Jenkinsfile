// pipeline {
//     agent any

//     environment {
//         AWS_REGION = 'us-east-1'
//         ECR_REPOSITORY = 'sample'
//         ECR_REGISTRY = "686255964186.dkr.ecr.${AWS_REGION}.amazonaws.com"
//         ECS_CLUSTER = 'new-app'
//         ECS_SERVICE = 'demoservice'
//         TASK_DEFINITION_FAMILY = 'jenkins_task'
//         TASK_DEFINITION_FILE = 'task-definition.json'
//         IMAGE_TAG = "${env.BUILD_NUMBER}"
//         IMAGE_URI = "${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}"
//     }

//     stages {
//         stage('Checkout Code') {
//             steps {
//                 script {
//                     checkout scm
//                 }
//             }
//         }

//         stage('Authenticate with AWS ECR') {
//             steps {
//                 script {
//                     withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws_key', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
//                         sh """
//                             echo "Logging into AWS ECR..."
//                             aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}
//                         """
//                     }
//                 }
//             }
//         }
//         stage('Build and Push Docker Image using Kaniko') {
//             steps {
//                 script {
//                     withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws_key', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
//                         sh """
//                             echo "Building and pushing Docker image using Kaniko..."
//                             /kaniko/executor --context /var/jenkins_home/workspace/jenkins-demo --dockerfile /var/jenkins_home/workspace/jenkins-demo/Dockerfile --destination=${IMAGE_URI} --no-push=false
//                         """
//                     }
//                 }
//             }
//         }

//         stage('Update Task Definition') {
//             steps {
//                 script {
//                     sh """
//                         echo "Updating ECS Task Definition..."
//                         jq '.containerDefinitions[0].image = "${IMAGE_URI}"' ${TASK_DEFINITION_FILE} > updated-task-definition.json
//                     """
//                 }
//             }
//         }

//         stage('Register ECS Task Definition') {
//             steps {
//                 script {
//                     withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws_key', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
//                         sh """
//                             echo "Registering updated ECS Task Definition..."
//                             aws ecs register-task-definition --cli-input-json file://updated-task-definition.json
//                         """
//                     }
//                 }
//             }
//         }

//         stage('Update ECS Service') {
//             steps {
//                 script {
//                     withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws_key', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
//                         def newRevision = sh(script: """
//                             aws ecs describe-task-definition --task-definition ${TASK_DEFINITION_FAMILY} --query "taskDefinition.revision" --output text
//                         """, returnStdout: true).trim()

//                         echo "Updating ECS service with task definition revision: ${TASK_DEFINITION_FAMILY}:${newRevision}"

//                         sh """
//                             aws ecs update-service --cluster ${ECS_CLUSTER} --service ${ECS_SERVICE} --task-definition ${TASK_DEFINITION_FAMILY}:${newRevision} --force-new-deployment
//                         """
//                     }
//                 }
//             }
//         }
//     }
// }








pipeline {
    agent {
    environment {
        AWS_REGION = 'us-east-1'  // Set your AWS region
        ECR_REPOSITORY = 'sample'  
        ECS_CLUSTER = 'new-app'  
        ECS_SERVICE = 'demoservice' 
        TASK_DEFINITION_FAMILY = 'jenkins_task' 
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
                    def newRevision = sh(script: """
                            aws ecs describe-task-definition --task-definition ${TASK_DEFINITION_FAMILY} --query "taskDefinition.revision" --output text
                        """, returnStdout: true).trim()

                        echo "Updating ECS service with task definition revision: ${TASK_DEFINITION_FAMILY}:${newRevision}"

                        sh """
                            aws ecs update-service --cluster ${ECS_CLUSTER} --service ${ECS_SERVICE} --task-definition ${TASK_DEFINITION_FAMILY}:${newRevision} --force-new-deployment
                        """
                    }
                }
            }   
        }
    }
}
}


