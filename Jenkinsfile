pipeline {
  agent any
  tools { nodejs 'NodeJS-18' }   // must match the NodeJS installation name in Jenkins
  environment {
    AWS_REGION = 'ap-south-1'
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Install') {
      steps {
        sh 'npm ci'
      }
    }
    stage('Build') {
      steps {
        // adjust if your build name differs
        sh 'ng build --configuration production || npm run build -- --configuration=production'
        archiveArtifacts artifacts: 'dist/**', allowEmptyArchive: false
      }
    }
    stage('Deploy to S3') {
      steps {
        script {
          def bucket = (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'master') ? 'gunadev-angular-prod' :
                       (env.BRANCH_NAME == 'staging' ? 'gunadev-angular-stage' : 'gunadev-angular-dev')

          // if your dist has nested folder like dist/angular-sample, change the source accordingly
          sh "aws s3 sync dist/ s3://${bucket} --delete --region ${AWS_REGION}"
          
          // optional: CloudFront invalidation for prod (uncomment & set CF_ID)
          // if (bucket == 'gunadev-angular-prod') {
          //   sh "aws cloudfront create-invalidation --distribution-id YOUR_CF_ID --paths '/*'"
          // }
        }
      }
    }
  }
  post {
    success { echo \"Deployed ${env.BRANCH_NAME} -> ${bucket}\" }
    failure { echo 'Build or deploy failed' }
  }
}
