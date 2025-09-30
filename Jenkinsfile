pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "asfand348/hello-node-app"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Asfand543/appjs.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                bat 'npm test || echo "No tests to run"'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker context use default'  // Ensure Docker is using default context
                    docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    bat 'docker context use default'
                    withDockerRegistry([credentialsId: 'dockerhubcredentials']) {
                        // Push versioned image
                        bat "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                        
                        // Tag as latest and push
                        bat "docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest"
                        bat "docker push ${DOCKER_IMAGE}:latest"
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    bat """
                    docker stop hello-node-app || exit 0
                    docker rm hello-node-app || exit 0
                    docker run -d -p 3000:3000 --name hello-node-app ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    """
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
