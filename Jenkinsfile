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
        DIR = "/var/lib/jenkins/workspace/new-job/usecase"
    }

    stages {
        stage('checkout SCM'){
            steps{
                cleanWs() 
               sh "git clone https://github.com/kittuhk/usecase.git"
            }
        }
        stage('Terraform Init') {
            steps {
                dir("${DIR}") {
                    sh 'terraform init'
                }
            }
        }

        stage('Terraform Action') {
            when {
                expression { params.ACTION == 'apply' }
            }
            steps {
                dir("${DIR}") {
                    sh 'terraform plan'
                    sh 'terraform apply --auto-approve'
                }
            }
        }

stage('Terraform Destroy') {
            when { expression { params.TERRAFORM_ACTION == 'destroy' } }
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GCLOUD_KEY')]) {
                    sh '''
                        gcloud auth activate-service-account --key-file=$GCLOUD_KEY
                        export GOOGLE_APPLICATION_CREDENTIALS=$GCLOUD_KEY
                        cd usecase
                        terraform init
                        terraform destroy --auto-approve
                    '''
                }
            }
       }
    }
}
