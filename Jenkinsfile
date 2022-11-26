pipeline {
    agent any
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/multisanjeev/industry_grade_1.git'
            }
        }
        
        stage('compile project') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('test and package project') {
            steps {
                sh "mvn test package"
            }
            post {
              success {
                junit 'target/surefire-reports/**/*.xml'
              }
            }
        }
        
        stage('code coverage report') {
            steps {
                jacoco()
            }
        }
        
        stage ("Generate docker build") {
            steps {
                sh "docker build . -t sanjeev0001/edureka_project_1"
            }
        }
        
        stage ('Push image on container') {
            steps {
                withCredentials([string(credentialsId: 'docker_credentials', variable: 'dockerPwd')]) {
                    sh 'docker login -u sanjeev0001 -p $dockerPwd'
                }
                //sh "docker push sanjeev0001/edureka_project_1:0.0.${env.BUILD_ID}"
                sh "docker push sanjeev0001/edureka_project_1"
            }
            
        }
        stage('ansible job') {
            steps {
                sh 'ansible-playbook deployment_playbook.yml'
            }
        }
    }
}