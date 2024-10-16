pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/chinmaya10000/solar-system.git'
                }
            }
        }
        stage('Secret Scanning with Gitleaks') {
            steps {
                // Run Gitleaks inside a Docker container
                script {
                    sh 'gitleaks detect --source=. -v --report-path=gitleaks-report.json'
                }
            }
        }
    }

    post {
        always {
            // Archive the Gitleaks report in Jenkins
            archiveArtifacts artifacts: 'gitleaks-report.json'
        }
        failure {
            echo 'Secrets detected by Gitleaks. Please check the report.'
        }
    }
}