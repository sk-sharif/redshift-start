pipeline {
    agent any

    stages {
        stage('Run Gitleaks') {
            steps {
                // Run Gitleaks
                script {
                    def gitleaksCmd = 'gitleaks detect --source . --report-format json --report-path gitleaks-report.json'
                    def gitleaksStatus = sh(script: gitleaksCmd, returnStatus: true)

                    if (gitleaksStatus != 0) {
                        // If Gitleaks detects secrets, mark the build as unstable
                        currentBuild.result = 'UNSTABLE'
                        echo "Gitleaks found potential secrets. Check the report at gitleaks-report.json"
                    } else {
                        echo "No secrets detected by Gitleaks."
                    }
                }
            }
        }

        stage('Build') {
            steps {
                // Your build steps here
                echo 'Building...'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
