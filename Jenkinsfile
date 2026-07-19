pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "mallikarjun46/portfolio-website"
        DOCKER_TAG = "latest"
    }
    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning from GitHub...'
                git branch: 'main',
                    url: 'https://github.com/Mallikarjun46/portfolio-website.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing to Docker Hub...'
                withCredentials([usernamePassword(
                    credentialsId: 'ebffc5a2-27a3-4e7f-ae68-82f710cba57d',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push mallikarjun46/portfolio-website:latest
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'
                sh "kubectl apply -f k8s/deployment.yaml"
                sh "kubectl apply -f k8s/service.yaml"
                sh "kubectl rollout status deployment/portfolio-website"
            }
        }
    }
    post {
        success { echo '✅ Portfolio website deployed successfully!' }
        failure { echo '❌ Pipeline failed!' }
    }
}

