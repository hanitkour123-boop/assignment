pipeline {
    agent any

    environment {
        BASE_IMAGE = "hshar/webapp"
        DOCKER_IMAGE = "hanitkour123/webapp:latest"
        DOCKERHUB_CREDS = "dockerhub-creds"  // Docker Hub credentials ID in Jenkins
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                // Pull base image
                sh "docker pull ${BASE_IMAGE}"
                // Build new image tagging with your Docker Hub repo
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    // Login to Docker Hub
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    // Push image
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
    }
}

