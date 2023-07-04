pipeline {
    agent any

    environment {
        ECR_URL = '854171615125.dkr.ecr.us-east-1.amazonaws.com/abhayecr'
        REPO_NAME = 'Final-Project'
    }

    stages {
        stage('ECR authentication and Docker login') {
            steps {

                sh '''
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 854171615125.dkr.ecr.us-east-1.amazonaws.com
               cd yolo5
                docker build -t abhayecr .
                docker tag abhayecr:latest 854171615125.dkr.ecr.us-east-1.amazonaws.com/abhayecr:latest
                docker push 854171615125.dkr.ecr.us-east-1.amazonaws.com/abhayecr:latest
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                cd yolo5
                docker build -t ${REPO_NAME} .
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                docker tag ${REPO_NAME} ${ECR_URL}/${REPO_NAME}:0.0.${BUILD_NUMBER}
                docker push ${ECR_URL}/${REPO_NAME}:0.0.${BUILD_NUMBER}
                '''
            }
        }

        stage('Trigger Deploy') {
            steps {
                build job: 'Yolo5Deploy', wait: false, parameters: [
                    string(name: 'YOLO5_IMAGE_URL', value: "${ECR_URL}/${REPO_NAME}:0.0.${BUILD_NUMBER}")
                ]
            }
        }
    }
}
