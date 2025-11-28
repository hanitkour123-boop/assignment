pipeline {
    agent any

    stages {

        stage('Check Branch') {
            steps {
                echo "Current branch is: ${env.BRANCH_NAME}"
            }
        }

        stage('Build') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t hshar/webapp .'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh 'echo Testing the application...'
            }
        }

        stage('Prod') {
            when {
                expression { env.BRANCH_NAME == 'master' }
            }
            steps {
                echo "Deploying to production..."
                sh 'echo Production deployment done'
            }
        }
    }
}

