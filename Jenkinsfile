pipeline {
    agent any
    stages {
        // stage('Checkout') {
        //     steps {
        //         checkout scm
        //     }
        // }
        stage('Checkout Code') {
            steps {
                echo "Checking out code from GitHub..."
                withCredentials([string(credentialsId: 'GIT_ACCESS_TOKEN', variable: 'GIT_ACCESS_TOKEN')]) {
                    sh '''#!/bin/bash
                        set -e
                        if [ -d "edc-jenkins-pipeline-" ]; then
                            rm -rf edc-jenkins-pipeline-
                        fi
                        echo "Cloning repository..."
                        git clone https://${GIT_ACCESS_TOKEN}@github.com/Sanjaypramod/edc-jenkins-pipeline-.git
                    '''
                }
            }
        }
        stage('Build and Deploy Service A') {
            when {
                changeset "**/app1/**"
            }
            steps {
                echo "Changes detected in service-a. Building and deploying..."
                // Add your build and deployment commands here
            }
        }
        stage('Build and Deploy Service B') {
            when {
                changeset "**/app2/**"
            }
            steps {
                echo "Changes detected in service-b. Building and deploying..."
                // Add your build and deployment commands here
            }
        }
    }
}
