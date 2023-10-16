pipeline {
    agent any

    environment {
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}" // Use build number as the Docker image tag
        FRONTEND_IMAGE = "frontend-app:${DOCKER_IMAGE_TAG}"
        BACKEND_IMAGE = "backend-app:${DOCKER_IMAGE_TAG}"
    }

    stages {
        stage('Front-End Build') {
            steps {
                checkout scm
                script {
                    // Front-end build
                    dir('frontend') {
                        sh 'npm install'
                        sh 'npm run build'
                    }
                }
            }
        }

        stage('Back-End Build') {
            steps {
                checkout scm
                script {
                    // Back-end build
                    dir('backend') {
                        sh 'npm install'
                        // Additional build steps for the back-end, like running database migrations
                    }
                }
            }
        }

        stage('Database Setup') {
            steps {
                sh 'docker run -d --name my-database -e POSTGRES_PASSWORD=mysecretpassword postgres:latest'
            }
        }

        stage('Build and Deploy Containers') {
            steps {
                // Build Docker images for the front-end and back-end
                sh "docker build -t ${FRONTEND_IMAGE} frontend"
                sh "docker build -t ${BACKEND_IMAGE} backend"
                
                // Run Docker containers for the front-end, back-end, and link to the database
                sh "docker run -d --name frontend-container -p 80:80 ${FRONTEND_IMAGE}"
                sh "docker run -d --name backend-container -p 3000:3000 --link my-database ${BACKEND_IMAGE}"
            }
        }

        stage('Integration Tests') {
            steps {
                // Perform integration tests here if needed
            }
        }
    }

    post {
        success {
            // Cleanup: Stop and remove Docker containers and the database
            sh 'docker stop frontend-container backend-container my-database'
            sh 'docker rm frontend-container backend-container my-database'
        }
        failure {
            // Handle failures here (e.g., cleanup)
        }
    }
}
