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

        stage('Checkout Usecase Repo') {
            steps {
                cleanWs()
                git branch: 'main', url: 'https://github.com/kittuhk/usecase.git'
            }
        }

        stage('Terraform Apply') {
            when {
                expression { params.ACTION == 'apply' }
            }
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GCLOUD_KEY')]) {
                    dir('/var/lib/jenkins/workspace/new-job/usecase') {
                        sh '''
                            gcloud auth activate-service-account --key-file=$GCLOUD_KEY
                            export GOOGLE_APPLICATION_CREDENTIALS=$GCLOUD_KEY
                            terraform init
                            terraform apply --auto-approve
                        '''
                    }
                }
            }
        }

        stage('Terraform Destroy') {
            when {
                expression { params.ACTION == 'destroy' }
            }
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GCLOUD_KEY')]) {
                    dir('/var/lib/jenkins/workspace/new-job/usecase') {
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
