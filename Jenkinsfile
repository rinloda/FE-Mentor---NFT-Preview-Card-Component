pipeline {
    agent any
    environment {
        DOCKER_TAG="${GIT_BRANCH.tokenize('/').pop()}-${GIT_COMMIT.substring(0,7)}"
        DOCKER_REGISTRY="8.142.49.158:8080"
        DOCKER_IMAGE="nft-component"
    }
    stages {
        stage('Docker build'){
            steps {
                echo 'Message: Docker will start to building an image based on Dockerfile'
                sh 'sudo -s docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
                echo 'Message: Docker will start to tagging an image based on Git branch to Docker Registry'
                sh 'sudo -s docker tag -t ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}'
                echo 'Message: Tagging with the latest tag'
                sh 'sudo -s docker tag -t ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest'
            }
        }

        stage('Docker push'){
            steps {
                echo 'Message: Starting to push with git-branch tag'
                sh 'sudo -s docker push ${DOCKER_REGISTRY}/${DOCKER_IAMGE}:${DOCKER_TAG}'
                echo 'Message: Starting to push with latest tag'
                sh 'sudo -s docker push ${DOCKER_REGISTRY}/${DOCKER_IAMGE}:latest'
                echo 'Message: Remove an image with git-branch tag in Server1'
                sh 'sudo -s docker image rm ${DOCKER_IAMGE}:${DOCKER_TAG}'
            }
        }
    }
}