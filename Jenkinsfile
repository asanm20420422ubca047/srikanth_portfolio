pipeline {
  agent any
  environment {
    FRONTEND_IMAGE = "${env.DOCKERHUB_USERNAME ?: 'local'}/srikanth-portfolio-frontend:${env.BUILD_NUMBER ?: 'latest'}"
    BACKEND_IMAGE = "${env.DOCKERHUB_USERNAME ?: 'local'}/srikanth-portfolio-backend:${env.BUILD_NUMBER ?: 'latest'}"
  }
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Install and build') { steps {
      dir('frontend') { sh 'npm install'; sh 'npm run build' }
      dir('backend') { sh 'npm install' }
    } }
    stage('Validate API') { steps { sh 'node --check backend/server.js'; sh 'node --check backend/controllers/contactController.js' } }
    stage('Build images') { steps { sh 'docker build -t "$FRONTEND_IMAGE" ./frontend'; sh 'docker build -t "$BACKEND_IMAGE" ./backend' } }
    stage('Deploy locally') { steps {
      sh 'docker compose down || true'
      sh 'docker compose up -d --build'
      sh 'for i in $(seq 1 30); do if curl -fsS http://localhost:4000/api/health; then exit 0; fi; sleep 2; done; exit 1'
      sh 'curl -fsS http://localhost:8080/ >/dev/null'
    } }
  }
  post { always { echo 'Pipeline complete. Local Compose deployment uses ports 8080 and 4000.' } }
}
