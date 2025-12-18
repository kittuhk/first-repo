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
        DIR = "/var/lib/jenkins/workspace/new-job/usecase"
    }

    stages {

        stage('checkout SCM') {
            steps {
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

        stage('Terraform Apply') {
            when {
                expression { params.TERRAFORM_ACTION == 'apply' }
            }
            steps {
                      sh  'terraform init'
                      sh  'terraform apply --auto-approve'
                }
            }
        }

        stage('Terraform Destroy') {
            when {
                expression { params.TERRAFORM_ACTION == 'destroy' }
            }
            steps {
                dir("${DIR}") {
                    withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GCLOUD_KEY')]) {
                        sh '''
                            gcloud auth activate-service-account --key-file=$GCLOUD_KEY
                            export GOOGLE_APPLICATION_CREDENTIALS=$GCLOUD_KEY
                            terraform init
                            terraform destroy --auto-approve
                        '''
                    }
                }
            }
        }
    }
}
