pipeline {
    agent any

    // environment {
        
        // DOCKER_IMAGE = "your-dockerhub-username/devops-project:${env.BUILD_NUMBER}"
      // REGISTRY_CREDENTIALS = 'dockerhub-credentials-id' // Replace with your Docker Hub credentials ID in Jenkins
    // }

    stages {

        stage('Docker Build') {
            steps {
                    sh "docker build Docker/. -t react-app:${env.BUILD_NUMBER}"
                    sh "docker run -d -p 3000:80 react-app:${env.BUILD_NUMBER}"
                
            }
        }

        // stage('Docker Push') {
        //     steps {
        //         script {
        //             // Login to Docker Hub
        //           //  docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials-id') {
        //                 // Push the Docker image
        //              //   docker.image("${DOCKER_IMAGE}").push()
        //             }
        //         }
        //     }
         }

       
    

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
