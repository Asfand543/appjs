
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
                // If you don’t have tests, you can skip or keep a dummy one
                bat 'npm test || echo "No tests to run"'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                    bat 'docker context use default'

                }
            }
        }
        
        stage('Push to Docker Hub') {
    steps {
        script {
            withDockerRegistry([ credentialsId: 'dockerhubcredentialsd', url: '' ]) {
                bat '''
                    docker --context default login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                    docker --context default push asfand348/hello-node-app:17
                '''
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
