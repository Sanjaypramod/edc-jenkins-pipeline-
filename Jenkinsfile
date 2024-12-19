pipeline {
    agent any
    environment {
        DOCKER_CREDENTIALS_ID = 'DOCKER_HUB' // Using your configured DockerHub credentials
        DOCKERHUB_NAMESPACE = 'sanjaypramod' // Replace with your DockerHub namespace
        DOCKERHUB_REPO = 'edc_test' // Repository name in DockerHub
    }
    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out code from GitHub..."
                checkout scm
            }
        }
        stage('Build and Push App1') {
            when {
                changeset "app1/**, **/lerna.json, **/package.json, **/.gitignore, **/README.md"
            }
            steps {
                echo "Changes detected in App1. Building and pushing Docker image..."
                dir('app1') {
                    script {
                        def appName = "app1"
                        def imageTag = "${DOCKERHUB_NAMESPACE}/${DOCKERHUB_REPO}:${appName}-${env.BUILD_NUMBER}"
                        
                        // Build Docker image
                        sh """
                        docker build -t ${imageTag} -f Dockerfile .
                        """
                        
                        // Push Docker image
                        withDockerRegistry([credentialsId: DOCKER_CREDENTIALS_ID]) {
                            sh """
                            docker push ${imageTag}
                            """
                        }
                    }
                }
            }
        }
        stage('Build and Push App2') {
            when {
                changeset "app2/**, **/lerna.json, **/package.json, **/.gitignore, **/README.md"
            }
            steps {
                echo "Changes detected in App2. Building and pushing Docker image..."
                dir('app2') {
                    script {
                        def appName = "app2"
                        def imageTag = "${DOCKERHUB_NAMESPACE}/${DOCKERHUB_REPO}:${appName}-${env.BUILD_NUMBER}"
                        
                        // Build Docker image
                        sh """
                        docker build -t ${imageTag} -f Dockerfile .
                        """
                        
                        // Push Docker image
                        withDockerRegistry([credentialsId: DOCKER_CREDENTIALS_ID]) {
                            sh """
                            docker push ${imageTag}
                            """
                        }
                    }
                }
            }
        }
        stage('Notify Changes in Root') {
            when {
                changeset "**/.gitignore, **/README.md, **/lerna.json, **/package.json"
            }
            steps {
                echo "Changes detected in root files. Consider rebuilding both services."
            }
        }
    }
}
