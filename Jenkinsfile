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

        stage('Run Tests') {
            steps {
                bat 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker context use default'
                    bat "docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    bat 'docker context use default'
                    withDockerRegistry([credentialsId: 'dockerhubcredentialsd']) {
                        // Push versioned tag
                        bat "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                        
                        // Tag and push latest
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
                    docker run -d -p 3000:3000 --name hello-node-app ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Visit http://localhost:3000"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
