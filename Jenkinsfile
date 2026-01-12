pipeline {
  agent any

  environment {
    PATH = "${HOME}/.local/bin:/opt/homebrew/bin:${PATH}"
    AWS_DEFAULT_REGION = "us-east-1"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Install Tools') {
      steps {
        sh '''
          python3 --version
          pip3 --version
          pip3 install --user cfn-lint checkov
          echo "PATH=$PATH"
        '''
      }
    }

    stage('Lint CloudFormation') {
      steps { sh 'cfn-lint cfn/template.yaml' }
    }

    stage('Security Scan (Checkov)') {
      steps { sh 'checkov -f cfn/template.yaml' }
    }

    stage('Deploy to AWS') {
      steps {
        sh '''
          set -e
          aws --version
          aws sts get-caller-identity --region "$AWS_DEFAULT_REGION"

          BUCKET_NAME="srini-secure-bucket-$(date +%s)"
          aws cloudformation deploy \
            --stack-name cfn-secure-s3-jenkins \
            --template-file cfn/template.yaml \
            --parameter-overrides BucketName="$BUCKET_NAME" \
            --capabilities CAPABILITY_NAMED_IAM \
            --region "$AWS_DEFAULT_REGION"
        '''
      }
    }
  }
}
