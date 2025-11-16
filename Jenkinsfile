pipeline {
  agent any
  environment {
    AWS_REGION = 'ap-south-1'
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }
    stage('Build Angular') {
      steps {
        sh 'ng build --configuration production'
      }
    }
    stage('Deploy to S3') {
      steps {
        script {
          def bucket = ''
          if (env.BRANCH_NAME == 'main') {
            bucket = 'gunadev-angular-prod'
          } else if (env.BRANCH_NAME == 'staging') {
            bucket = 'gunadev-angular-stage'
          } else {
            bucket = 'gunadev-angular-dev'
          }

          sh "aws s3 sync dist/ s3://${bucket} --delete --region ${AWS_REGION}"
        }
      }
    }
  }
}
