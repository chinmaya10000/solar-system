pipeline {
    agent any

    environment {
        IMAGE_NAME = 'chinmayapradhan/solar-system'
        IMAGE_TAG = 'v9'
        GITHUB_TOKEN = credentials('github-token')
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
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }
        stage('Clone/Pull Repo') {
            steps {
                script {
                    if (fileExists('gitops-argocd')) {

                        echo 'Cloned repo already exists - Pulling latest changes'

                        dir("gitops-argocd") {
                            sh 'git pull'
                        }
                    } else {
                        echo 'Repo does not exists - Cloning the repo'
                        sh 'git clone https://github.com/chinmaya10000/gitops-argocd.git'
                    }
                }
            }
        }
        stage('Update Manifest') {
            steps {
                script {
                    dir('gitops-argocd/jenkins-demo') {
                        sh "sed -i 's#image: chinmayapradhan/.*#image: ${IMAGE_NAME}:${IMAGE_TAG}#g' deployment.yaml"
                    }
                }
            }
        }
        stage('Commit and Push') {
            steps {
                script {
                    dir('gitops-argocd/jenkins-demo') {
                        sh 'git config --global user.email "jenkins@ci.com"'
                        sh 'git config --global user.name "jenkins"'
                        sh "git remote set-url origin https://${GITHUB_TOKEN}@github.com/chinmaya10000/gitops-argocd.git"
                        sh 'git checkout feature/argocd-gitops'
                        sh 'git add .'
                        sh 'git commit -m "Updated image version for Build - $IMAGE_TAG"'
                        sh 'git push origin feature/argocd-gitops'
                    }
                }
            }
        }
        stage('Raise PR') {
            steps {
                script {
                    def prTitle = "Update image version to ${IMAGE_TAG}"
                    def prBody = "This PR updates the image version to ${IMAGE_TAG} for deployment."

                    sh """
                        curl -X POST -H "Authorization: token ${GITHUB_TOKEN}" \
                        -H "Accept: application/vnd.github.v3+json" \
                        https://api.github.com/repos/chinmaya10000/gitops-argocd/pulls \
                        -d '{
                            "title": "${prTitle}",
                            "body": "${prBody}",
                            "head": "feature/argocd-gitops",
                            "base": "main"
                        }'
                    """
                }
            }
        }
    }
}