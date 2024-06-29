pipeline {
    agent any 

    environment {
        REPO_URL = 'https://github.com/Naveendi4087/Group22-DevOps.git'
        BRANCH = 'main'
        DOCKER_REGISTRY = 'thashara'
        DOCKER_USER = 'thashara'
        DOCKER_PASS = credentials('Group-22pass') 
        APP_NAME = 'Group22-DevOps'
    }

    stages { 
        stage('SCM Checkout') {
            steps {
                retry(3) {
                    git branch: "${BRANCH}", url: "${REPO_URL}"
                }
            }
        }
        stage('Build backend Docker Image') {
            steps {  
                dir('backend') {
                    sh 'docker build -t ${DOCKER_REGISTRY}/backendv2:${BUILD_NUMBER} .'
                }
            }
        }
        stage('Build frontend Docker Image') {
            steps {
                dir('frontend') {
                    sh 'docker build -t ${DOCKER_REGISTRY}/frontendv2:${BUILD_NUMBER} .'
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                script {
                    sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                }
            }
        }
        stage('Push backend Image') {
            steps {
                sh 'docker push ${DOCKER_REGISTRY}/backendv2:${BUILD_NUMBER}'
            }
        }
        stage('Push frontend Image') {
            steps {
                sh 'docker push ${DOCKER_REGISTRY}/frontendv2:${BUILD_NUMBER}'
            }
        }
        stage('Terraform Init') {
            steps {
                dir('infra') {
                    sh 'terraform init'
                }
            }
        }
        stage('Terraform Apply') {
            steps {
                dir('infra') {
                    sh 'terraform apply --auto-approve'
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}