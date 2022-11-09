pipeline {
    agent any
    tools {
      maven 'maven3'
    }
    
    environment {
      DOCKER_TAG = getVersion()
    }

    stages {
        stage('SCM') {
            steps {
                git credentialsId: 'git', 
                    url: 'https://github.com/multisanjeev/industry_grade_1.git'
            }
        }
        
        stage('Maven Build') {
            steps {
                sh "mvn clean"
                sh "mvn package"
            }
        }
        
        stage('Docker build') {
            steps {
                sh "docker build . -t sanjeev0001/industry_grade1:0.0.${DOCKER_TAG}"
            }
        }
        
        stage('Docker push image to hub') {
            steps {
               withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                sh "docker login  -u sanjeev0001 -p ${dockerHubPwd}"
            }
            
            sh "docker push sanjeev0001/industry_grade1:0.0.${DOCKER_TAG}" 
            }
        }
        
        stage('ansible docker deploy') {
            steps {
                ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: '-e DOCKER_TAG=${DOCKER_TAG}', installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion() {
    def commitHead = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHead
}
