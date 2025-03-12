pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'your_dockerhub_username/app1' // استبدل باسم مستودعك
        IMAGE_TAG = "v${env.BUILD_NUMBER}" // رقم إصدار الصورة
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/IbrahimAdell/App1.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $DOCKER_HUB_REPO:$IMAGE_TAG .
                '''
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-credentials', url: '']) {
                    sh '''
                    docker push $DOCKER_HUB_REPO:$IMAGE_TAG
                    '''
                }
            }
        }
        stage('Delete Local Image') {
            steps {
                sh '''
                docker rmi $DOCKER_HUB_REPO:$IMAGE_TAG
                '''
            }
        }
        stage('Update Deployment YAML') {
            steps {
                sh '''
                sed -i "s|image: .*|image: $DOCKER_HUB_REPO:$IMAGE_TAG|" deployment.yaml
                '''
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline executed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
