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
        REACT_APP_THEME = 'theme2'
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
            withEnv(['THEME=${REACT_APP_THEME}']) {
                sh "npm install"
                sh "npm run build"
                sh "docker build -t ${CONTAINER_NAME}:${DOCKER_TAG} ."
            }
        }
    }
}
stage('Publish Test Reports') {
            steps {
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: false,
                    keepAll: false,
                    reportDir: '/var/lib/jenkins/workspace/Insure-Me/target/surefire-reports',
                    reportFiles: 'index.html',
                    reportName: 'HTML Report',
                    reportTitles: '',
                    useWrapperFileDirectly: true
                ])
            }
        }

        stage('Deploy to Bastion Host') {
            steps {
                script {
                    sshagent(['your-ssh-credentials']) {
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
        }

        stage('Deploy to App Server') {
            steps {
                script {
                    sshagent(['your-ssh-credentials']) {
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
        }

        stage('Deploy to DB Server') {
            steps {
                script {
                    sshagent(['your-ssh-credentials']) {
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

        stage('Push to Docker Registry') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
                    sh "docker login -u $dockerUser -p $dockerPassword"
                    sh "docker tag ${CONTAINER_NAME}:${DOCKER_TAG} $dockerUser/${CONTAINER_NAME}:${DOCKER_TAG}"
                    sh "docker push $dockerUser/${CONTAINER_NAME}:${DOCKER_TAG}"
                }
            }
        }
    }
}
