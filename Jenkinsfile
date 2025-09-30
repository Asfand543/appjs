
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "your-dockerhub-username/hello-node-app"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Asfand543/appjs.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                // If you don’t have tests, you can skip or keep a dummy one
                bat 'npm test || echo "No tests to run"'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        docker.image("${DOCKER_IMAGE}:${BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    bat '''
                    docker stop hello-node-app || true
                    docker rm hello-node-app || true
                    docker run -d -p 3000:3000 --name hello-node-app ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
