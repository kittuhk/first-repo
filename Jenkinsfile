pipeline {
    agent any

    parameters {
        choice(
            name: 'ACTION',
            choices: ['apply', 'destroy'],
            description: 'Select Terraform action'
        )
    }

    stages {

        stage('Checkout Usecase Repo') {
            steps {
                cleanWs()
                git branch: 'main', url: 'https://github.com/kittuhk/usecase.git'
            }
        }

        stage('Terraform Init') {
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GCLOUD_KEY')]) {
                    dir('usecase') {
                        sh '''
                            gcloud auth activate-service-account --key-file=$GCLOUD_KEY
                            export GOOGLE_APPLICATION_CREDENTIALS=$GCLOUD_KEY
                            terraform init
                        '''
                    }
                }
            }
        }

        stage('Terraform Apply') {
            when {
                expression { params.ACTION == 'apply' }
            }
            steps {
                dir('usecase') {
                    sh 'terraform apply --auto-approve'
                }
            }
        }

        stage('Terraform Destroy') {
            when {
                expression { params.ACTION == 'destroy' }
            }
            steps {
                dir('usecase') {
                    sh 'terraform destroy --auto-approve'
                }
            }
        }
    }
}
