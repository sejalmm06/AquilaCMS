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
                    // Install npm dependencies
                    sh "npm install"

                    // Build the specified theme (REACT_APP_THEME)
                    //sh "npm run build $REACT_APP_THEME"

                    // Build the Docker image
                    sh "docker build -t ${CONTAINER_NAME}:${DOCKER_TAG} ."
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
                    def containerName = "aquilacms"
                    def tag = "latest"

                    sh "docker tag ${containerName}:${tag} $DOCKER_HUB_USER/${containerName}:${tag}"
                    sh "docker push $DOCKER_HUB_USER/${containerName}:${tag}"
                    echo "Image push complete"
                }
            }
        }

         stage('Deploy to Bastion Host') {
            steps {
                script {
                    ansiblePlaybook(
                        credentialsId: 'private-key',
                        disableHostKeyChecking: true,
                        installation: 'ansible',
                        inventory: "bastion_host=${BASTION_HOST} app_server=${APP_SERVER} db_server=${DB_SERVER}",
                        playbook: "${ANSIBLE_PLAYBOOK}"
                    )
                }
            }
        }

        stage('Deploy to App Server') {
            steps {
                script {
                    ansiblePlaybook(
                        credentialsId: 'private-key',
                        disableHostKeyChecking: true,
                        installation: 'ansible',
                        inventory: "app_server=${APP_SERVER}",
                        playbook: "${ANSIBLE_PLAYBOOK}"
                    )
                }
            }
        }

        stage('Deploy to DB Server') {
            steps {
                script {
                    ansiblePlaybook(
                        credentialsId: 'private-key',
                        disableHostKeyChecking: true,
                        installation: 'ansible',
                        inventory: "db_server=${DB_SERVER}",
                        playbook: "${ANSIBLE_PLAYBOOK}"
                    )
                }
            }
        }
    }
}
