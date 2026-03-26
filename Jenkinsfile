pipeline {
    agent any

    environment {
        IMAGE_NAME = "nginx-app"
        DOCKERHUB_REPO = "viktor3arsovski/nginx-app"
        CONTAINER_NAME = "nginx-app-container"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Tag Image') {
            steps {
                script {
                    sh "docker tag ${IMAGE_NAME}:latest ${DOCKERHUB_REPO}:${BRANCH_NAME}-${BUILD_NUMBER}"
                    sh "docker tag ${IMAGE_NAME}:latest ${DOCKERHUB_REPO}:${BRANCH_NAME}-latest"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'DockerHub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKERHUB_REPO}:${BRANCH_NAME}-${BUILD_NUMBER}
                        docker push ${DOCKERHUB_REPO}:${BRANCH_NAME}-latest
                        docker logout
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
    }
}