pipeline {
    agent any

    environment {
        IMAGE_NAME = "ganeshdatti76/nodejs-app"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image..."
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                echo "Logging into DockerHub..."
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing Docker Image..."
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying to Kubernetes..."
                sh '''
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully 🚀'
        }
        failure {
            echo 'Pipeline failed ❌ Check logs.'
        }
    }
}
