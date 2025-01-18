pipeline {
    agent any

    environment {
        IMAGE_NAME = 'chinmayapradhan/solar-system'
        IMAGE_TAG = 'v9'
    }

    stages {
        stage('Unit Tests') {
            steps {
                script {
                    echo 'Implement unit tests if applicable.'
                    echo 'This stage is a sample placeholder'
                }
            }
        }
        stage('Build and Push Image') {
            withCredentials([usernamePassword(credentialsId: 'docker-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "echo $PASS | docker login -u $USER --password-stdin"
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }
}