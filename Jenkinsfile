pipeline {
  agent { label 'saps-Agent' }

  environment {
    IMAGE_NAME = "django-notes-app"
    CONTAINER_NAME = "django-notes-container"
    PORT = "8000"
  }

  stages {

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME:latest .'
      }
    }

    stage('Stop Old Container') {
      steps {
        sh '''
        docker stop $CONTAINER_NAME || true
        docker rm $CONTAINER_NAME || true
        '''
      }
    }

    stage('Run New Container') {
      steps {
        sh '''
        docker run -d \
        --name $CONTAINER_NAME \
        -p 8000:8000 \
        $IMAGE_NAME:latest
        '''
      }
    }

  }
}
