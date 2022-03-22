pipeline {
    agent any
    environment {
        DOCKER_TAG="${GIT_BRANCH.tokenize('/').pop()}-${GIT_COMMIT.substring(0,7)}"
        DOCKER_REGISTRY="18.142.49.158:8080"
        DOCKER_IMAGE="nft-component"
        SERVER3="18.142.47.71"
    }
    stages {
        stage('Clone stages') {
            steps {
                git credentialsId: 'github_id', url: 'https://github.com/rinloda/FE-Mentor-NFT-Preview-Card-Component.git'
            }
        }
        stage('Docker build'){
            steps {
                echo 'Message: Docker will start to building an image based on Dockerfile'
                sh 'sudo docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
                echo 'Message: Docker will start to tagging an image based on Git branch to Docker Registry'
                sh 'sudo docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}'
                echo 'Message: Tagging with the latest tag'
                sh 'sudo docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest'
            }
        }

        stage('Docker push'){
            steps {
                echo 'Message: Starting to push with git-branch tag'
                sh 'sudo docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}'
                echo 'Message: Starting to push with latest tag'
                sh 'sudo docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest'
                echo 'Message: Remove an image with git-branch tag in Server1'
                sh 'sudo docker image rm ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}'
            }
        }

        stage('SSH Server3'){
            steps{
                sshagent(['ssh-remote-server3']) {
                sh 'ssh -o StrictHostKeyChecking=no -l ubuntu ${SERVER3} docker pull ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest'
                // sh 'ssh -o StrictHostKeyChecking=no -l ubuntu ${SERVER3} docker rm -f ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}'
                sh 'ssh -o StrictHostKeyChecking=no -l ubuntu ${SERVER3} docker run -d -p 80:80 ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest'
                }
            }
        }
    }
}