def containerName="aquilacms"
def tag="latest"
def dockerHubUser="sejalmm06"
def httpPort="8084"

node {
    stage('Checkout') {
        checkout scm
    }
    stage('Build'){
        sh "mvn clean package"
    }
    stage('Publish Test Reports') {
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
    stage("Image Prune"){
         sh "docker image prune -a -f"
    }
    stage('Image Build'){
        sh "docker build -t $containerName:$tag --no-cache ."
        echo "Image build complete"
    }
    stage('Push to Docker Registry'){
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
            sh "docker login -u $dockerUser -p $dockerPassword"
            sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
            sh "docker push $dockerUser/$containerName:$tag"
            echo "Image push complete"
     }
    }
    stage('Run App') {
    ansiblePlaybook credentialsId: 'private-key', disableHostKeyChecking: true, installation: 'ansible', inventory: 'hosts', playbook: 'ansible-playbook.yml'
            }
    }
}
