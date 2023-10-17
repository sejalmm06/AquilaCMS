pipeline {
    agent any

    environment {
        ANSIBLE_PLAYBOOK = "ansible-playbook.yml"
        BASTION_HOST = "3.110.157.9"
        APP_SERVER = "10.0.2.211"
        DB_SERVER = "10.0.4.127"
        CONTAINER_NAME = "aquilacms"
        DOCKER_TAG = "latest"
        DOCKER_HUB_USER = "sejalmm06"
        REACT_APP_THEME = 'default_theme_2'
        DOCKER_COMPOSE_FILE = 'docker-compose.yml' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Package') {
            steps {
                script {
              def themeName = params.THEME_NAME
            sh "npm install"                            
            //sh "npm run build default_theme_2"  
                    //Build the Docker image
                  sh "docker-compose -f $DOCKER_COMPOSE_FILE build"
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
                    sh "echo $dockerPassword | docker login -u $dockerUser --password-stdin"
                }
            }
        }

        stage('Docker Tag and Push') {
            steps {
                script {
                  sh "docker-compose -f $DOCKER_COMPOSE_FILE push"
                    echo "Image push complete"
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    // Deploy the application using Docker Compose
                    sh "docker-compose -f $DOCKER_COMPOSE_FILE up -d"
                }
            }
        }
 stage('Deploy with Ansible') {
            steps {
                // Deploy the application using Ansible
                sh "ansible-playbook -i ${ANSIBLE_PLAYBOOK} -e CONTAINER_NAME=${CONTAINER_NAME} -e DOCKER_TAG=${DOCKER_TAG}"
                echo "Application deployment with Ansible completed"
            }
        }
    }
}
