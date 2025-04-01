pipeline {
    // Run on any available agent
    agent any

    // Environment variables
    environment {
        DOCKERHUB_USERNAME = 'bhadaneunnati1110'  // Your Docker Hub username
        APP_NAME = 'python-app'                   // Name of your app/image
    }

    stages {
        // Stage 1: Checkout code from GitHub
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    credentialsId: 'github-credentials', 
                    url: 'https://github.com/UnnatiBhadane/PythonA.git'
                sh 'ls -la'  // Debug: List files
            }
        }

        // Stage 2: Build Docker Image
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKERHUB_USERNAME}/${APP_NAME}:${env.BUILD_ID}")
                    sh 'docker images'  // Debug: List images
                }
            }
        }

        // Stage 3: Push to Docker Hub
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        def appImage = docker.image("${DOCKERHUB_USERNAME}/${APP_NAME}:${env.BUILD_ID}")
                        appImage.push()          // Push with build ID (e.g., bhadaneunnati1110/python-app:1)
                        appImage.push('latest')  // Push as latest
                    }
                    sh 'docker images'
                }
            }
        }

        // Stage 4: Clean Up
        stage('Clean Up') {
            steps {
                sh "docker rmi ${DOCKERHUB_USERNAME}/${APP_NAME}:${env.BUILD_ID}"
                sh "docker rmi ${DOCKERHUB_USERNAME}/${APP_NAME}:latest"
            }
        }
    }

    post {
        success {
            echo "Python pipeline completed successfully! Image pushed to Docker Hub."
        }
        failure {
            echo "Python pipeline failed. Check the console output for details."
        }
    }
}

