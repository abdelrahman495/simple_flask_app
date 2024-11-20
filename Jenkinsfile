pipeline {
    agent any
    
    environment {
        IMAGE_NAME = 'abdelrahman495/simple-flask-app'
        IMAGE_TAG = 'latest'
    }
    
    stages {
        stage('Test the application') {
            steps {
                script {
                    withPythonEnv('/usr/bin/python3'){
                        sh 'pip install bandit'
                        sh 'bandit -r app.py --exit-zero'
                    }
                }
            }
        }
        
        stage('Create an image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }
        
        stage('Scan the image') {
            steps {
                script {
                    sh 'trivy --quiet ${IMAGE_NAME}:${IMAGE_TAG}'
                }
            }
        }
        
        stage('Upload the image') {
            steps {
                script {
                    withDockerRegistry([url: 'https://index.docker.io/v1/', credentialsId: 'dockerhub-credentials']) {
                        docker.image("${IMAGE_NAME}:${IMAGE_TAG}").push()
                    }
                }
            }
        }
    }
}

