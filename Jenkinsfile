pipeline {
  agent any

  environment {
    IMAGE_NAME = "deepaksingla022/chat-app"
  }

  stages {
    stage('Clone Repository') {
      steps {
        git branch: 'main', url: 'https://github.com/deepaksingla022/real-time-chat-ci-cd.git'
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
        script {
          withCredentials([usernamePassword(credentialsId: 'github-http', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
            docker.withRegistry('https://index.docker.io/v1/', 'github-http') {
              dockerImage.push('latest')
            }
          }
        }
      }
    }

    stage('Remove Old Container') {
      steps {
        sh '''
          docker rm -f chat-app-container || true
        '''
      }
    }

    stage('Run New Container') {
      steps {
        sh '''
          docker run -d -p 3000:3000 --name chat-app-container deepaksingla022/chat-app:latest
        '''
      }
    }
  }

  post {
    failure {
      echo '❌ Deployment failed.'
    }
    success {
      echo '✅ Deployment successful.'
    }
  }
}
