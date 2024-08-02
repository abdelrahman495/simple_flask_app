pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        IMAGE_NAME = 'abdelrahman495/new-flask-app'
    }
    
    stages {
        stage('Test App') {
            steps {
                script {
                    sh 'pip install bandit'
                    sh 'bandit -r .'
                }
            }
        }
        
        stage('Build Image') {
            steps {
                script {
                    sh 'docker build -t $IMAGE_NAME:latest .'
                }
            }
        }
        
        stage('Scan Image') {
            steps {
                script {
                    sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock -v $HOME/Library/Caches:/root/.cache/ aquasec/trivy image $IMAGE_NAME:latest'
                }
            }
        }
        
        stage('Push Image') {
            steps {
                script {
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }
    }
}
