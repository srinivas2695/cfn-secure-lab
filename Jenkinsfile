pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
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
      steps {
        sh '~/.local/bin/cfn-lint cfn/template.yaml'
      }
    }

    stage('Security Scan (Checkov)') {
      steps {
        sh '~/.local/bin/checkov -f cfn/template.yaml'
      }
    }
  }
}
