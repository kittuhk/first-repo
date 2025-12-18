pipeline {
    agent any

    parameters {
        choice(
            name: 'ACTION',
            choices: ['apply', 'destroy'],
            description: 'Select what Terraform should do'
        )
    }

    environment {
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
        DIR = "usecase"
    }

    stages {

        stage('Checkout SCM') {
            steps {
                cleanWs()
                git url: 'https://github.com/kittuhk/usecase.git'
            }
        }

        stage('Terraform Init') {
            steps {
                dir("${DIR}") {
                    sh 'terraform init'
                }
            }
        }

        stage('Terraform Apply') {
            when {
                expression { params.ACTION == 'apply' }
            }
            steps {
                dir("${DIR}") {
                    sh 'terraform apply --auto-approve'
                }
            }
        }

        stage('Terraform Destroy') {
            when {
                expression { params.ACTION == 'destroy' }
            }
            steps {
                dir("${DIR}") {
                    sh 'terraform destroy --auto-approve'
                }
            }
        }
    }
}
