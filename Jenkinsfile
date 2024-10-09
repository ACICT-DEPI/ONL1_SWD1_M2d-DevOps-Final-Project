pipeline {
    agent any

  //  environment {
    //    DOCKER_IMAGE = "your-dockerhub-username/devops-project:${env.BUILD_NUMBER}"
     //   REGISTRY_CREDENTIALS = 'dockerhub-credentials-id' // Replace with your Docker Hub credentials ID in Jenkins
   // }

    stages {
         stage('Install Dependencies') {
            steps {
                // Install dependencies using yarn
                sh 'yarn install'
            }
        }

        stage('Test') {
            steps {
                // Run tests
                sh 'yarn test'
            }
        }
        stage('Docker Build') {
            steps {
                sh "docker build Docker/. -t react-app:${env.BUILD_NUMBER}"
            }
        }

        stage('Docker deploy') {
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
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
