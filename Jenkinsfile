pipeline {
    agent any

    parameters {
        choice(
            name: 'ACTION',
            choices: ['apply', 'destroy'],
            description: 'Select what Terraform should do'
        )
    }
    stages {

        stage('Checkout SCM') {
            steps {
                cleanWs()
                git url: 'https://github.com/kittuhk/usecase.git'
            }
        }
        stage('Terraform Apply') {
            when { expression { params.TERRAFORM_ACTION == 'apply' } }
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GCLOUD_KEY')]) {
                    sh '''
                        gcloud auth activate-service-account --key-file=$GCLOUD_KEY
                        export GOOGLE_APPLICATION_CREDENTIALS=$GCLOUD_KEY
                        cd /var/lib/jenkins/workspace/new-job/usecase
                        terraform init
                        terraform apply --auto-approve
                    '''
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
                        cd /var/lib/jenkins/workspace/new-job/usecase
                        terraform init
                        terraform destroy --auto-approve
                    '''
                }
            }
        }
    }
}
