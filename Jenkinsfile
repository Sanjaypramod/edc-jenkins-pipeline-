pipeline {
    agent any
    environment {
        DOCKER_REGISTRY = "sanjaypramod/monorepo"  // Your Docker Hub repository
    }
    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out code from GitHub..."
                withCredentials([string(credentialsId: 'git-access-token', variable: 'GIT_ACCESS_TOKEN')]) {
                    sh """
                        git clone https://$GIT_ACCESS_TOKEN@github.com/Sanjaypramod/monorepo.git
                        cd monorepo
                    """
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    def apps = ['app1', 'app2'] // List of apps in the monorepo
                    apps.each { app ->
                        echo "Building Docker image for ${app}..."
                        sh """
                            cd monorepo/${app}
                            docker build -t ${DOCKER_REGISTRY}:${app} .
                        """
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    script {
                        def apps = ['app1', 'app2']
                        apps.each { app ->
                            echo "Pushing Docker image for ${app}..."
                            sh """
                                docker push ${DOCKER_REGISTRY}:${app}
                            """
                        }
                    }
                }
            }
        }

        stage('Deploy to k3s') {
            steps {
                withCredentials([file(credentialsId: 'KUBECONFIG', variable: 'KUBECONFIG_FILE')]) {
                    script {
                        def apps = ['app1', 'app2']
                        sh 'export KUBECONFIG=$KUBECONFIG_FILE'
                        apps.each { app ->
                            echo "Deploying ${app} to k3s..."
                            sh """
                                kubectl apply -f monorepo/${app}/k8s/deployment.yaml
                                kubectl apply -f monorepo/${app}/k8s/service.yaml
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "All applications have been successfully deployed to k3s."
        }
        failure {
            echo "Pipeline failed! Check the logs."
        }
    }
}
