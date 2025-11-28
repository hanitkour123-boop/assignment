pipeline {
    agent any

    environment {
        BASE_IMAGE = "hshar/webapp"
        DOCKER_IMAGE = "hanitkour123/webapp:latest"
        DOCKERHUB_CREDS = "dockerhub-creds"  // Docker Hub credentials ID in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.image(BASE_IMAGE).pull() // Pull base image
                    // Build new image tagging with your Docker Hub repo
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKERHUB_CREDS) {
                        docker.image(DOCKER_IMAGE).push()
                    }
                }
            }
        }
    }
}

