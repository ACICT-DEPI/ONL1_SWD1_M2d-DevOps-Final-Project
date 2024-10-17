pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mohamedesmael/devops_final_project:${env.BUILD_NUMBER}"
        LATEST_IMAGE = "mohamedesmael/devops_final_project:latest"
        REGISTRY_CREDENTIALS = 'dockerhub-credentials-id' // Docker Hub credentials ID
        AWS_CREDENTIALS = 'aws-credentials-id' // AWS credentials ID from Jenkins
        TERRAFORM_DIR = 'Devops-Project/terraform' // Replace with actual Terraform directory
    }

    stages {
        stage('Check and Free Port 3000') {
            steps {
                script {
                    // Check if any container is using port 3000
                    def containerID = sh(
                        script: "docker ps --filter 'publish=3000' --format '{{.ID}}'",
                        returnStdout: true
                    ).trim()

                    // If a container is found, stop and remove it
                    if (containerID) {
                        echo "Stopping container with ID ${containerID} using port 3000"
                        sh "docker stop ${containerID}"
                        sh "docker rm ${containerID}"
                    } else {
                        echo "No container found using port 3000"
                    }
                }
            }
        }
        
        stage('Docker Build') {
            steps {
                // Build the Docker image
                sh "docker build Docker/. -t ${DOCKER_IMAGE}"
            }
        }

        stage('Docker Tag as Latest') {
            steps {
                // Tag the image as latest
                sh "docker tag ${DOCKER_IMAGE} ${LATEST_IMAGE}"
            }
        }

        stage('Docker Push') {
            steps {
                // Push the images to Docker Hub
                withCredentials([usernamePassword(credentialsId: REGISTRY_CREDENTIALS, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push ${DOCKER_IMAGE}
                        docker push ${LATEST_IMAGE}
                    '''
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                // Deploy the Docker container
                sh "docker run -d -p 3000:80 ${LATEST_IMAGE}"
            }
        }

        stage('Run Terraform') {
            steps {
                // Run Terraform commands in the specified directory
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS]]) {
                    dir(TERRAFORM_DIR) {
                        sh '''
                            terraform init
                            terraform apply -auto-approve
                        '''
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
