pipeline {
    agent any

    environment {
        TF_IN_AUTOMATION = 'true'
        TF_CLI_ARGS     = '-no-color'
        SSH_CRED_ID     = 'pranav_Id'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Terraform Init & Inspect Vars') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'AWS_pranav',
                     accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                     secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']
                ]) {
                    sh '''
                        terraform init
                        echo "Using ${BRANCH_NAME}.tfvars"
                        cat ${BRANCH_NAME}.tfvars
                    '''
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'AWS_pranav',
                     accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                     secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']
                ]) {
                    sh "terraform plan -var-file=${BRANCH_NAME}.tfvars"
                }
            }
        }

        stage('Validate Apply') {
            when {
                branch 'dev'
            }
            steps {
                input message: 'Approve Terraform Apply for DEV environment?'
            }
        }
    }
}
