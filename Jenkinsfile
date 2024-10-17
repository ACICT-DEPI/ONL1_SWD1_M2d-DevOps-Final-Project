pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mohamedesmael/devops_final_project:${env.BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = 'dockerhub-credentials-id' // The ID for Docker Hub credentials in Jenkins
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
                sh "docker build Docker/. -t ${DOCKER_IMAGE}"
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: REGISTRY_CREDENTIALS, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push ${DOCKER_IMAGE}
                    '''
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                sh "docker run -d -p 3000:80 ${DOCKER_IMAGE}"
            }
        }
    }

    post {
        success {
            mail to: 'abdelrahman.naser958@gmail.com',
                subject: "Successful Pipeline: ${currentBuild.fullDisplayName}",
                body: "The build succeeded: ${env.BUILD_URL}"
        }
        failure {
            mail to: 'abdelrahman.naser958@gmail.com',
                subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
