pipeline {
    agent any
    
    environment {
        TERRAFORM_HOME = tool 'TerraformInstalled'
        PATH = "${TERRAFORM_HOME}:${PATH}"
    }
    
    parameters {
        booleanParam(name: 'apply', defaultValue: false, description: 'Apply changes or just plan')
        booleanParam(name: 'destroy', defaultValue: false, description: 'Destroy environment')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    extensions: [[$class: 'CleanBeforeCheckout']],
                    userRemoteConfigs: [[url: 'https://github.com/Bodiok007/TerraformAppForJenkins.git']]
                ])
            }
        }
        
        stage('Verify Terraform Installation') {
            steps {
                sh 'echo $PATH'
                sh 'terraform version'
            }
        }
        
        stage('Init') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                                     string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                        ansiColor('xterm') {
                            sh '''
                                export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                                export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                                terraform init
                            '''
                        }
                    }
                }
            }
        }
        
        stage('Plan') {
            when {
                expression { params.apply == false && params.destroy == false }
            }
            steps {
                script {
                    withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                                     string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                        ansiColor('xterm') {
                            sh '''
                                export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                                export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                                terraform plan -lock=false
                            '''
                        }
                    }
                }
            }
        }
        
        stage('Apply') {
            when {
                expression { params.apply == true && params.destroy == false }
            }
            steps {
                script {
                    withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                                     string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                        ansiColor('xterm') {
                            sh '''
                                export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                                export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                                terraform apply -auto-approve
                            '''
                        }
                    }
                }
            }
        }
        
        stage('Destroy') {
            when {
                expression { params.destroy == true }
            }
            steps {
                withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                                 string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                        export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                        export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                        terraform destroy -auto-approve
                    '''
                }
            }
        }
    }
}