pipeline {
    agent any

   environment {
       GROOVY_HOME = tool name: 'Terraform', type: 'com.cloudbees.jenkins.plugins.customtools.CustomTool'
    }   
    options {
        ansiColor('xterm')
    }
    parameters {
        choice(
            choices: ['preview' , 'apply' , 'show', 'preview-destroy' , 'destroy'],
            description: 'Terraform action to apply',
            name: 'action')
        choice(
            choices: ['master' , 'dev' , 'qa', 'staging'],
            description: 'Choose branch to build and deploy',
            name: 'branch')
    }
    
    stages {
        stage('Git Checkout'){
         steps {
		    git url: 'https://github.com/SubhakarKotta/aws-eks-rds-terraform',branch: "${params.branch}"
         }
	    }

        stage('init') {
            steps {
                sh '${GROOVY_HOME}/terraform version'
                sh '${GROOVY_HOME}/bin/terraform init -backend-config="bucket=${ACCOUNT}-tfstate" -backend-config="key=${TF_VAR_stack_name}/terraform.tfstate" -backend-config="region=us-west-2"'
            }
        }
        stage('validate') {
            when {
                expression { params.action == 'preview' || params.action == 'apply' || params.action == 'destroy' }
            }
            steps {
                sh '${GROOVY_HOME}/terraform validate -var aws_profile=${AWS_PROFILE}'
            }
        }
        stage('preview') {
            when {
                expression { params.action == 'preview' }
            }
            steps {
                sh '${GROOVY_HOME}/terraform plan -var aws_profile=${AWS_PROFILE}'
            }
        }
        stage('apply') {
            when {
                expression { params.action == 'apply' }
            }
            steps {
                sh 'terraform plan -out=plan -var aws_profile=${AWS_PROFILE}'
                sh '${GROOVY_HOME}/terraform apply -auto-approve plan'
            }
        }
        stage('show') {
            when {
                expression { params.action == 'show' }
            }
            steps {
                sh '${GROOVY_HOME}/terraform show'
            }
        }
        stage('preview-destroy') {
            when {
                expression { params.action == 'preview-destroy' }
            }
            steps {
                sh '${GROOVY_HOME}/terraform plan -destroy -var aws_profile=${AWS_PROFILE}'
            }
        }
        stage('destroy') {
            when {
                expression { params.action == 'destroy' }
            }
            steps {
                sh '${GROOVY_HOME}/terraform destroy -force -var aws_profile=${AWS_PROFILE}'
            }
        }
    }
}