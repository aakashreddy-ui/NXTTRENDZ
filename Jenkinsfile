pipeline {
  agent any

  environment {
    FRONTEND_IMAGE = 'aakash2611/nxttrendz-frontend'
    BACKEND_IMAGE = 'aakash2611/nxttrendz-backend'
  }

  stages {
    stage('Build frontend image') {
      steps {
        sh '''
          docker build \
            --tag "$FRONTEND_IMAGE:$BUILD_NUMBER" \
            --tag "$FRONTEND_IMAGE:latest" \
            ./frontend
        '''
      }
    }

    stage('Build backend image') {
      steps {
        sh '''
          docker build \
            --tag "$BACKEND_IMAGE:$BUILD_NUMBER" \
            --tag "$BACKEND_IMAGE:latest" \
            ./backend
        '''
      }
    }

    stage('Push images to Docker Hub') {
      steps {
        withCredentials([
          usernamePassword(
            credentialsId: 'dockerhub-creds',
            usernameVariable: 'DOCKERHUB_USERNAME',
            passwordVariable: 'DOCKERHUB_TOKEN'
          )
        ]) {
          sh '''
            set +x
            export DOCKER_CONFIG="$(mktemp -d)"
            trap 'docker logout >/dev/null 2>&1 || true; rm -rf "$DOCKER_CONFIG"' EXIT
            printf '%s' "$DOCKERHUB_TOKEN" | docker login \
              --username "$DOCKERHUB_USERNAME" \
              --password-stdin
            docker push "$FRONTEND_IMAGE:$BUILD_NUMBER"
            docker push "$FRONTEND_IMAGE:latest"
            docker push "$BACKEND_IMAGE:$BUILD_NUMBER"
            docker push "$BACKEND_IMAGE:latest"
          '''
        }
      }
    }
  }
}
