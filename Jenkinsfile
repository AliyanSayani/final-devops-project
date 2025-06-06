pipeline {
  agent any

  environment {
    PATH = "/usr/local/sbin:/usr/bin/terraform:${env.PATH}"
    TF_IN_AUTOMATION = "true"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Terraform Init') {
      steps {
        dir('terraform') {
          sh 'terraform init'
        }
      }
    }

    stage('Terraform Apply') {
      steps {
        dir('terraform') {
          sh 'terraform apply -auto-approve'
        }
      }
    }

    stage('Ansible Deploy') {
      steps {
        dir('ansible') {
          sh 'ansible-playbook -i inventory install_web.yml'
        }
      }
    }

    stage('Verify') {
      steps {
        // Replace with your actual public IP or terraform output variable in the future
        sh 'curl http://223.123.108.1'
      }
    }
  }
}
