pipeline {
  agent { label 'saps' }

  environment {
    AWS_REGION = "us-west-1"
    ECR_REPO   = "django-notes-app"
    ACCOUNT_ID = "138256873419"
    IMAGE_TAG  = "v${BUILD_NUMBER}"
  }

  stages {

    stage('Checkout') {
      steps {
        git 'https://github.com/saps27ss/django-notes-app.git'
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t $ECR_REPO:$IMAGE_TAG .'
      }
    }

    stage('Login to ECR') {
      steps {
        sh '''
        aws ecr get-login-password --region $AWS_REGION \
          | docker login --username AWS --password-stdin \
          $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
        '''
      }
    }

    stage('Tag & Push') {
      steps {
        sh '''
        docker tag $ECR_REPO:$IMAGE_TAG \
          $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG

        docker push \
          $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG
        '''
      }
    }

    stage('Deploy to EKS') {
      steps {
        sh '''
        sed -i "s|image:.*|image: $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG|" k8s/deployment.yaml
        kubectl apply -f k8s/deployment.yaml
        '''
      }
    }
  }
}

