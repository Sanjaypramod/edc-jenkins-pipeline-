pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out code from GitHub..."
                checkout scm
                sh """
                echo "Listing files in workspace:"
                ls -R
                """
            }
        }
        stage('Debug Changeset') {
            steps {
                echo "Checking for changed files..."
                sh """
                git diff --name-only HEAD~1 HEAD || echo "No changes detected"
                """
            }
        }
        stage('Build and Push app1') {
            when {
                anyOf {
                    changeset "app1/**" // Match all changes in app1 directory
                    changeset "**/lerna.json" // Match root-level lerna.json
                    changeset "**/package.json" // Match root-level package.json
                    changeset "**/.gitignore" // Match root-level .gitignore
                    changeset "**/README.md" // Match root-level README.md
                }
            }
            steps {
                script {
                    def appName = "app1"
                    // Use Jenkins credentials for DockerHub
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        def imageTag = "${DOCKER_USERNAME}/edc_test:${appName}-${env.BUILD_NUMBER}"

                        // Docker login
                        sh """
                        echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin
                        """

                        // Build Docker image
                        dir('app1') { // Navigate to app1 directory
                            sh """
                            docker build -t ${imageTag} -f Dockerfile .
                            """
                        }

                        // Push Docker image
                        sh """
                        docker push ${imageTag}
                        """
                    }
                }
            }
        }

        stage('Build and Push app2') {
            when {
                anyOf {
                    changeset "app2/**" // Match all changes in app1 directory
                    changeset "**/lerna.json" // Match root-level lerna.json
                    changeset "**/package.json" // Match root-level package.json
                    changeset "**/.gitignore" // Match root-level .gitignore
                    changeset "**/README.md" // Match root-level README.md
                }
            }
            steps {
                script {
                    def appName = "app2"
                    // Use Jenkins credentials for DockerHub
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        def imageTag = "${DOCKER_USERNAME}/edc_test:${appName}-${env.BUILD_NUMBER}"

                        // Docker login
                        sh """
                        echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin
                        """

                        // Build Docker image
                        dir('app2') { // Navigate to app2 directory
                            sh """
                            docker build -t ${imageTag} -f Dockerfile .
                            """
                        }

                        // Push Docker image
                        sh """
                        docker push ${imageTag}
                        """
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