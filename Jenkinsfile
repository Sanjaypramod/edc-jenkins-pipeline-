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

        stage('Build and Push Applications') {
            parallel {
                stage('Build and Push app1') {
                    when {
                        anyOf {
                            changeset "app1/**"
                            changeset "**/lerna.json"
                            changeset "**/package.json"
                            changeset "**/.gitignore"
                            changeset "**/README.md"
                        }
                    }
                    steps {
                        script {
                            def appName = "app1"
                            withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                                def imageTag = "${DOCKER_USERNAME}/edc_test:${appName}-${env.BUILD_NUMBER}"

                                sh """
                                echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin
                                """
                                dir('app1') {
                                    sh """
                                    docker build -t ${imageTag} -f Dockerfile .
                                    """
                                }
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
                            changeset "app2/**"
                            changeset "**/lerna.json"
                            changeset "**/package.json"
                            changeset "**/.gitignore"
                            changeset "**/README.md"
                        }
                    }
                    steps {
                        script {
                            def appName = "app2"
                            withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                                def imageTag = "${DOCKER_USERNAME}/edc_test:${appName}-${env.BUILD_NUMBER}"

                                sh """
                                echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin
                                """
                                dir('app2') {
                                    sh """
                                    docker build -t ${imageTag} -f Dockerfile .
                                    """
                                }
                                sh """
                                docker push ${imageTag}
                                """
                            }
                        }
                    }
                }

                stage('Build and Push app3') {
                    when {
                        anyOf {
                            changeset "app3/**"
                            changeset "**/lerna.json"
                            changeset "**/package.json"
                            changeset "**/.gitignore"
                            changeset "**/README.md"
                        }
                    }
                    steps {
                        script {
                            def appName = "app3"
                            withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                                def imageTag = "${DOCKER_USERNAME}/edc_test:${appName}-${env.BUILD_NUMBER}"

                                sh """
                                echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin
                                """
                                dir('app3') {
                                    sh """
                                    docker build -t ${imageTag} -f Dockerfile .
                                    """
                                }
                                sh """
                                docker push ${imageTag}
                                """
                            }
                        }
                    }
                }

                stage('Build and Push app4') {
                    when {
                        anyOf {
                            changeset "app4/**"
                            changeset "**/lerna.json"
                            changeset "**/package.json"
                            changeset "**/.gitignore"
                            changeset "**/README.md"
                        }
                    }
                    steps {
                        script {
                            def appName = "app4"
                            withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                                def imageTag = "${DOCKER_USERNAME}/edc_test:${appName}-${env.BUILD_NUMBER}"

                                sh """
                                echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin
                                """
                                dir('app4') {
                                    sh """
                                    docker build -t ${imageTag} -f Dockerfile .
                                    """
                                }
                                sh """
                                docker push ${imageTag}
                                """
                            }
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
