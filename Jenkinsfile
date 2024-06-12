pipeline {
    agent any
    
    environment {
        AWS_ACCESS_KEY_ID = 'AKIAU6GD3YTKQL5Z6D5Y'
        AWS_SECRET_ACCESS_KEY = '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'
        CLUSTER_IDENTIFIER = 'redshift-cluster-1'
        LAMBDA_FUNCTION_NAME = 'Redshift_Cluster_Start_Function'
        AWS_REGION = 'eu-central-1'
    }

    stages {
        stage('Trigger Lambda') {
            steps {
                script {
                    withAWS(region: "${env.AWS_REGION}", credentials: 'aws-credentials-id') {
                        sh '''
                        aws lambda invoke \
                            --function-name ${LAMBDA_FUNCTION_NAME} \
                            --payload '{}' \
                            response.json
                        '''
                    }
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
