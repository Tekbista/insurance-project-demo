pipeline {
    agent any

    stages {
        stage('git checkout') {
            steps {
                echo 'checking out the code from github'
                git 'https://github.com/Tekbista/insurance-project-demo.git'
            }
        }
        
        stage('maven build') {
            steps {
                echo 'building the project'
                sh 'mvn clean package'
            }
        }
        
        stage('build docker image') {
            steps {
                echo 'building docker image'
                sh 'docker build -t tekbista37545/insure-me:1.0 .'
            }
        }
        
        stage('push docker image to docker hub registry') {
            steps {
                echo 'pushing image to docker hub'
                withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerpwd')]) {
                    sh "docker login -u tekbista37545 -p ${dockerpwd}"
                    sh "docker push tekbista37545/insure-me:1.0"
                }
            }
        }
        
        stage('deploy to test environment'){
            steps{
                echo 'configuring test-server'
                ansiblePlaybook credentialsId: 'ssh-key', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-test-server.yml', vaultTmpPath: ''
            }
            
        }
        
        stage('git checkout selenium test code') {
            steps {
                dir("test"){
                    git branch: 'main', url: 'https://github.com/Tekbista/seleniumtest.git'
                }
                
            }
        }
        
        stage('run selenium test in the test env') {
            steps {
                sh "java -jar test/insureme.jar"
            }
        }
        
        stage('deploy to prod environment'){
            steps{
                echo 'configuring prod-server'
                ansiblePlaybook credentialsId: 'ssh-key', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-prod-server.yml', vaultTmpPath: ''
            }
            
        }
    }
}
