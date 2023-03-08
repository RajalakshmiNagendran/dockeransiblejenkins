pipeline {
    agent any
    tools {
      maven 'maven'
    }
    environment {
      DOCKER_TAG = getversion()
    }
    stages{
        stage('SCM'){
            steps{
                git 'https://github.com/RajalakshmiNagendran/dockeransiblejenkins.git'
            }
        }
        stage('Maven Build'){
            steps{
                sh 'mvn clean install'
            }
        }
        stage('Docker Build'){
            steps{
                sh 'docker build . -t 9841/testapp:${DOCKER_TAG}'
            }
        }
        stage('Docker Push'){
            steps{
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerpwd')]) {
                    sh 'docker login -u 9841 -p ${dockerpwd}'
                }
                sh 'docker push 9841/testapp:${DOCKER_TAG}'
            }
        }
        stage('Docker Deploy'){
            steps{
                ansiblePlaybook credentialsId: 'ansible-cred', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'Ansible2', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getversion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
