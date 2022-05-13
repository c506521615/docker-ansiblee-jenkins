pipeline{
    agent any
    tools {
      maven 'maven385'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git branch: 'main', credentialsId: 'github', 
                url: 'https://github.com/c506521615/docker-ansiblee-jenkins.git'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t c506521615/chandrikaapp:${DOCKER_TAG} "
            }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u c506521615 -p ${dockerHubPwd}"
                }
                
                sh "docker push c506521615/chandrikaapp:${DOCKER_TAG} "
            }
        }
        stage('Docker Deploy'){
            steps{
                ansiblePlaybook extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', playbook: 'ansibledeploy.yml'
            }
        }
    }
}


def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
