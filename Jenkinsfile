// File: Jenkinsfile
// Description: To define CI/CD pipeline for Docker Image Building and pushing, deploying to Kubernetes cluster

pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker')  // Retrieve DockerHub credentials
        BUILD_TIMESTAMP = "${new Date().format('yyyyMMddHHmmss')}" // Use a timestamp for image tagging
        
    }
    stages {
        stage("Build Survey Image") {
            steps {
                script {
                    // Fetch the current SCM and print the timestamp
                    checkout scm
                    sh 'echo ${BUILD_TIMESTAMP}'
                    
                    // Correctly login to DockerHub using the username and password
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                    
                    // Build the Docker image
                    def customImage = docker.build("yukta1327/hw2-docker-image:${BUILD_TIMESTAMP}")
                }
            }
        }
        stage("Push img to docker hub") {
            steps {
                script {
                    // Push the Docker image to DockerHub
                    sh "docker push yukta1327/hw2-docker-image:${BUILD_TIMESTAMP}"
                }
            }
        }
        stage("Deploy to kubernetes") {
            steps {
                // Use a Secret File credential for kubeconfig
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh 'kubectl set image deployment/hw2-deployment hw2-container=yukta1327/hw2-docker-image:${BUILD_TIMESTAMP} -n default'
                }
            }
        }
    }
}
