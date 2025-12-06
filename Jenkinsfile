pipeline {
  agent any

  environment {
    DOCKERHUB_REPO = "hanitkour/website-app"   // 
    IMAGE_TAG = "${env.BUILD_ID}"
    IMAGE_LATEST = "${DOCKERHUB_REPO}:latest"
    IMAGE_VERSION = "${DOCKERHUB_REPO}:${IMAGE_TAG}"
  }

  options {
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '20'))
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker image') {
      steps {
        script {
          docker.withRegistry('', 'dockerhub-creds') {
            // Build image (local docker required on agent)
            sh """
              docker build -t ${IMAGE_VERSION} .
              docker tag ${IMAGE_VERSION} ${IMAGE_LATEST}
            """
          }
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh '''
            echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
            docker push ${IMAGE_VERSION}
            docker push ${IMAGE_LATEST}
            docker logout
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        // Use kubeconfig secret file stored in Jenkins
        withCredentials([file(credentialsId: 'kubeconfig-file', variable: 'KUBECONFIG_FILE')]) {
          sh '''
            export KUBECONFIG=$KUBECONFIG_FILE
            # Option A: update image on existing deployment
            if kubectl get deployment website-app >/dev/null 2>&1; then
              kubectl set image deployment/website-app website-container=${IMAGE_LATEST} --record || true
              kubectl rollout status deployment/website-app --timeout=120s
            else
              # Option B: apply manifests (service + deployment)
              kubectl apply -f k8s/deployment.yaml
              kubectl apply -f k8s/service.yaml
              kubectl rollout status deployment/website-app --timeout=120s
            fi
          '''
        }
      }
    }

    stage('Smoke Test') {
      steps {
        script {
          // Simple readiness check (assumes NodePort 30008 available)
          sh '''
            echo "Waiting a bit for service..."
            sleep 5
            echo "Listing pods and services:"
            kubectl get pods -o wide || true
            kubectl get svc website-service || true
          '''
        }
      }
    }
  }

  post {
    success {
      echo "Pipeline succeeded: ${IMAGE_LATEST}"
    }
    failure {
      echo "Pipeline failed"
    }
  }
}

