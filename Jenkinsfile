pipeline {
    agent any
    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCESS_KEY_ID     = sh(script: "aws configure set region ${AWS_REGION} && aws ssm get-parameter --region ${AWS_REGION} --name /underbelle/AWS/AccessKey --with-decryption --query 'Parameter.Value' --output text", returnStdout: true).trim()
        AWS_SECRET_ACCESS_KEY = sh(script: "aws configure set region ${AWS_REGION} && aws ssm get-parameter --region ${AWS_REGION} --name /underbelle/AWS/SecretKey --with-decryption --query 'Parameter.Value' --output text", returnStdout: true).trim()
    }
    parameters {
        choice(name: 'action', choices: ['apply', 'destroy'], description: 'Select Apply or Destroy')
    }
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/underbelle237/demojenkins']]])
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Terraform Plan') {
            steps {
                sh 'terraform plan -out=tfplan'
            }
        }

        stage('Terraform Apply or Destroy') {
            steps {
                echo "Apply or Destroy is --> ${params.action}"
                sh "terraform ${params.action} -auto-approve"
            }
        }
    }
}
