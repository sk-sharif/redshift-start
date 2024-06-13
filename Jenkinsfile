pipeline {
    agent any
    
    environment {
        AWS_REGION = 'eu-central-1'
        LAMBDA_FUNCTION_NAME = 'Redshift_Cluster_Start_Function'
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
                    // Check if the response.json file exists using Jenkins-approved method
                    if (!fileExists('response.json')) {
                        error "Response file 'response.json' not found."
                    }
                    
                    // Parse the response using Groovy's JSON parser
                    def response = readFile('response.json')
                    def jsonResponse = new groovy.json.JsonSlurper().parseText(response)
                    
                    if (jsonResponse.StatusCode != 200) {
                        error "Lambda function failed with response status code: ${jsonResponse.StatusCode}"
                    } else {
                        echo "Lambda function succeeded: ${jsonResponse.body}"
                    }
                }
            }
        }
    }
}
