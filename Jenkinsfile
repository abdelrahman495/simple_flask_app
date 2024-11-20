pipeline {
    agent any
    
    environment {
        IMAGE_NAME = 'abdelrahman495/simple-flask-app'
        IMAGE_TAG = '0.2'
    }
    
    stages {
        stage('Test the application') {
            agent {
                docker {
                    image 'python:3.9.20-alpine3.20'
                    args '-u root -v .:/workspace'
                }
            }
            steps {
                script {
                    withPythonEnv('/usr/local/bin/python3') {
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
            agent {
                docker {
                    image 'aquasec/trivy:latest'
                    args '-u root --entrypoint=""'
                }
            }
            steps {
                script {
                    sh 'trivy image --quiet ${IMAGE_NAME}:${IMAGE_TAG}'
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

        stage('Deploy the application') {
            steps {
                script {
                    def flaskImage = docker.image("${IMAGE_NAME}:${IMAGE_TAG}")
                    flaskImage.run('--name my-flask-app -p 8081:5000')
                }
            }
        }   
    }
}

