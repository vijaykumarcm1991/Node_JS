pipeline {
  agent any

  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')  // Add this in Jenkins Credentials
    IMAGE_NAME = "vijaykumarcm/node_js"
    TAG = "${BUILD_NUMBER}"
  }

  stages {
    stage('Clone') {
      steps {
        git 'https://github.com/vijaykumarcm1991/Node_JS.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        dir('app') {
          sh 'docker build -t $IMAGE_NAME:$TAG .'
        }
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker push $IMAGE_NAME:$TAG
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh "sed 's|__IMAGE__|$IMAGE_NAME:$TAG|' k8s/deployment.yaml | kubectl apply -f -"
        sh 'kubectl apply -f k8s/service.yaml'
      }
    }
  }

// Post actions can be added here if needed
  post {
    success {
      echo 'Pipeline completed successfully!'
    }
    failure {
      echo 'Pipeline failed!'
    }
  }
}