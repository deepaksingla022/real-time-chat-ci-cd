pipeline {
  agent any

  environment {
    IMAGE_NAME = 'deepsingla022/chat-app'
  }

  stages {
    stage('Clone Repository') {
      steps {
        git 'https://github.com/deepaksingla022/real-time-chat-ci-cd.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("${IMAGE_NAME}")
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          script {
            docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
              dockerImage.push('latest')
            }
          }
        }
      }
    }

    stage('Remove Old Container') {
      steps {
        script {
          sh '''
            docker stop chat-app || true
            docker rm chat-app || true
          '''
        }
      }
    }

    stage('Run New Container') {
      steps {
        script {
          sh '''
            docker run -d -p 3000:3000 --name chat-app yourdockerhubusername/chat-app
          '''
        }
      }
    }
  }

  post {
    success {
      echo '🚀 Chat app deployed successfully!'
    }
    failure {
      echo '❌ Deployment failed.'
    }
  }
}
