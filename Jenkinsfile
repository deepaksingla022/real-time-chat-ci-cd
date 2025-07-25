pipeline {
  agent any

  environment {
    IMAGE_NAME = 'deepsingla022/chat-app'
  }

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
        withCredentials([usernamePassword(credentialsId: 'github-http', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          script {
            docker.withRegistry('https://index.docker.io/v1/', 'github-http') {
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
