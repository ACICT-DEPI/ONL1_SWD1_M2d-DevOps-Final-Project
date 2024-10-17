pipeline {
    agent any

    stages {
        stage('Check and free port 3000') {
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
                sh "docker build Docker/. -t react-app:${env.BUILD_NUMBER}"
            }
        }
        
        stage('Docker Deploy') {
            steps {
                sh "docker run -d -p 3000:80 react-app:${env.BUILD_NUMBER}"
            }
        }

        // Uncomment and configure the Docker Push stage if needed
        // stage('Docker Push') {
        //     steps {
        //         script {
        //             docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials-id') {
        //                 docker.image("${DOCKER_IMAGE}").push()
        //             }
        //         }
        //     }
        // }
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
