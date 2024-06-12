pipeline {
    agent any
    
    environment {
        LAMBDA_FUNCTION_NAME = 'Redshift_Cluster_Start_Function'
        AWS_REGION = 'eu-central-1'
    }

    stages {
        stage('Trigger Lambda') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'ef0e2e45-3545-44a2-b37d-84928a123942']]) {
                    sh '''
                    aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                    aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                    aws configure set region $AWS_REGION

                    aws lambda invoke \
                        --function-name ${LAMBDA_FUNCTION_NAME} \
                        --payload '{}' \
                        response.json
                    '''
                }
            }
        }

        stage('Check Lambda Response') {
            steps {
                script {
                    def response = readJSON file: 'response.json'
                    if (response.StatusCode != 200) {
                        error "Lambda function failed with response: ${response}"
                    } else {
                        echo "Lambda function succeeded: ${response}"
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
