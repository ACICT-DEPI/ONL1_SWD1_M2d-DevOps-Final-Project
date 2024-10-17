pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mohamedesmael/devops_final_project:${env.BUILD_NUMBER}"
        LATEST_IMAGE = "mohamedesmael/devops_final_project:latest"
        REGISTRY_CREDENTIALS = 'dockerhub-credentials-id' // Docker Hub credentials ID
        AWS_CREDENTIALS = 'aws-credentials-id' // AWS credentials ID from Jenkins
        TERRAFORM_DIR = '/project/terraform/Devops-Project/terraform' // Updated local directory for Terraform files
    }

    stages {
        stage('Preparation') {
            steps {
                script {
                    // Check if the Terraform directory exists
                    if (!fileExists(TERRAFORM_DIR)) {
                        error "Terraform directory ${TERRAFORM_DIR} does not exist or is not accessible."
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Log in to Docker Hub and push the latest image
                    docker.withRegistry('https://index.docker.io/v1/', REGISTRY_CREDENTIALS) {
                        sh "docker tag ${DOCKER_IMAGE} ${LATEST_IMAGE}"
                        sh "docker push ${LATEST_IMAGE}"
                    }
                }
            }
        }

        stage('Run Terraform') {
            steps {
                script {
                    // Run Terraform commands in the specified local directory
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS, accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        dir(TERRAFORM_DIR) {
                            sh '''
                                export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                                export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                                terraform init
                                terraform apply -auto-approve
                            '''
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            // Send success email notification
            mail to: 'abdelrahman.naser958@gmail.com, mohamed.2714104@gmail.com',
                subject: "Successful Pipeline: ${currentBuild.fullDisplayName}",
                body: "The build succeeded: ${env.BUILD_URL}"
        }
        failure {
            // Send failure email notification
            mail to: 'abdelrahman.naser958@gmail.com, mohamed.2714104@gmail.com',
                subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
