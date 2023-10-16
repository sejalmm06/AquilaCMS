pipeline {
    agent any

    environment {
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"
        FRONTEND_IMAGE = "frontend-app:${DOCKER_IMAGE_TAG}"
        BACKEND_IMAGE = "backend-app:${DOCKER_IMAGE_TAG}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Front-End Build') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Back-End Build') {
            steps {
                dir('backend') {
                    sh 'npm install'
                    // Additional build steps for the back-end, like running database migrations
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
                sh "docker build -t ${FRONTEND_IMAGE} frontend"
                sh "docker build -t ${BACKEND_IMAGE} backend"
                sh "docker run -d --name frontend-container -p 80:80 ${FRONTEND_IMAGE}"
                sh "docker
