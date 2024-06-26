pipeline {
    agent any
    tools {
        terraform 'terraform1'
    }
    environment {
        TF_HOME = tool('terraform1')
        TF_IN_AUTOMATION = "true"
        PATH = "$TF_HOME:$PATH"
    }
    stages {
        stage('Hello') {
            steps {
                bat label: '', script: '''
                    echo "hello world"
                '''
            }
        }

        stage('Test Access Key') {
            steps {
                withCredentials([string(credentialsId: 'access_key', variable: 'ARM_ACCESS_KEY')]) {
                    bat """
                        echo "Testing Access Key"
                        echo "Access Key: %ARM_ACCESS_KEY%"
                    """
                }
            }
        }

        stage('Terraform Init') {
            steps {
                ansiColor('xterm') {
                    withCredentials([
                        azureServicePrincipal(
                            credentialsId: 'azurelogin',
                            subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                            clientIdVariable: 'ARM_CLIENT_ID',
                            clientSecretVariable: 'ARM_CLIENT_SECRET',
                            tenantIdVariable: 'ARM_TENANT_ID'
                        ),
                        string(credentialsId: 'access_key', variable: 'ARM_ACCESS_KEY')
                    ]) {
                        bat '''
                            echo "Initialising Terraform"
                            terraform init -migrate-state -backend-config="access_key=%ARM_ACCESS_KEY%"
                        '''
                    }
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                ansiColor('xterm') {
                    withCredentials([
                        azureServicePrincipal(
                            credentialsId: 'azurelogin',
                            subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                            clientIdVariable: 'ARM_CLIENT_ID',
                            clientSecretVariable: 'ARM_CLIENT_SECRET',
                            tenantIdVariable: 'ARM_TENANT_ID'
                        ),
                        string(credentialsId: 'access_key', variable: 'ARM_ACCESS_KEY')
                    ]) {
                        bat '''
                            terraform validate
                        '''
                    }
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                ansiColor('xterm') {
                    withCredentials([
                        azureServicePrincipal(
                            credentialsId: 'azurelogin',
                            subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                            clientIdVariable: 'ARM_CLIENT_ID',
                            clientSecretVariable: 'ARM_CLIENT_SECRET',
                            tenantIdVariable: 'ARM_TENANT_ID'
                        ),
                        string(credentialsId: 'access_key', variable: 'ARM_ACCESS_KEY')
                    ]) {
                        bat '''
                            echo "Creating Terraform Plan"
                            terraform plan -var "client_id=%ARM_CLIENT_ID%" -var "client_secret=%ARM_CLIENT_SECRET%" -var "subscription_id=%ARM_SUBSCRIPTION_ID%" -var "tenant_id=%ARM_TENANT_ID%"
                        '''
                    }
                }
            }
        }

        stage('Waiting for Approval') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    input(message: "Deploy the infrastructure?")
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                ansiColor('xterm') {
                    withCredentials([
                        azureServicePrincipal(
                            credentialsId: 'azurelogin',
                            subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                            clientIdVariable: 'ARM_CLIENT_ID',
                            clientSecretVariable: 'ARM_CLIENT_SECRET',
                            tenantIdVariable: 'ARM_TENANT_ID'
                        ),
                        string(credentialsId: 'access_key', variable: 'ARM_ACCESS_KEY')
                    ]) {
                        bat '''
                            echo "Applying the plan"
                            terraform apply -auto-approve -var "client_id=%ARM_CLIENT_ID%" -var "client_secret=%ARM_CLIENT_SECRET%" -var "subscription_id=%ARM_SUBSCRIPTION_ID%" -var "tenant_id=%ARM_TENANT_ID%"
                        '''
                    }
                }
            }
        }
    }
}
