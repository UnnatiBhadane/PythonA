pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/UnnatiBhadane/PythonA.git', branch: 'main'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("python-app:${env.BUILD_ID}")
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    docker.image("python-app:${env.BUILD_ID}").inside {
                        sh 'echo "Tests passed (placeholder)"'
                    }
                }
            }
        }
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        docker.image("python-app:${env.BUILD_ID}").push()
                        docker.image("python-app:${env.BUILD_ID}").push('latest')
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh 'docker stop python-app || true && docker rm python-app || true'
                    sh 'docker run -d --name python-app -p 5000:5000 python-app:latest'
                }
            }
        }
    }
    post {
        always {
            sh 'docker system prune -f'
        }
    }
}

