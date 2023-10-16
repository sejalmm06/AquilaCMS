pipeline {
    parameters {
        choice(name: 'THEME', choices: 'default_theme_2', description: 'Select the theme to build')
    }

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

        stage('Back-End Build') {
            steps {
                sh 'npm install'
                // Additional build steps for the back-end, like running database migrations
                //sh 'npm install'
               // sh 'npm run build' // Replace with the actual command to build your backend
                //sh 'npm run migrate-database' // Replace with the command to run database migrations
                //h 'npm run start-backend' // Replace with the command to start your backend server
            }
        }

        stage('Database Setup') {
            steps {
                sh 'docker run -d --name my-database -e POSTGRES_PASSWORD=mysecretpassword postgres:latest'
            }
        }

        stage('Build and Deploy Containers') {
            steps {
                script {
                    def selectedTheme = "${params.THEME}"
                    // Define Docker build and run commands with the selected theme
                    dockerCommands = """
docker build -t ${FRONTEND_IMAGE} frontend --build-arg THEME=${selectedTheme}
docker build -t ${BACKEND_IMAGE} backend --build-arg THEME=${selectedTheme}
docker run -d --name frontend-container -p 80:80 ${FRONTEND_IMAGE}
docker run -d --name backend-container -p 3000:3000 --link my-database ${BACKEND_IMAGE}
"""
                    sh dockerCommands
                }
            }
        }
    }

    post {
        success {
            sh 'docker stop frontend-container backend-container my-database'
            sh 'docker rm frontend-container backend-container my-database'
        }
    }
}
