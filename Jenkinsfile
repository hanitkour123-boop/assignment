pipeline {
    agent any

    stages {

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
                branch 'master'
            }
            steps {
                echo "Deploying to production..."
                sh '''
                docker stop webapp || true
                docker rm webapp || true
                docker run -d -p 8080:80 --name webapp hshar/webapp
                '''
            }
        }
    }
}

