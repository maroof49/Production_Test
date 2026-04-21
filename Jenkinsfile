pipeline {

  agent any

  tools {
    maven 'maven'
  }
  environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub-credentials') 
        DOCKER_IMAGE = "maroof49/test_prod"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

  stages{

    stage ('build with maven'){
      steps {
        script{
          sh "mvn clean install"
        }
      }
    }

    stage ('build the docker image'){
      steps{
        script {
         sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ." 
        }
        }
      }

    stage ('push the docker image to dockerhub'){
      steps{
        script{
          // Login to Docker Hub
          sh "echo ${DOCKER_HUB_CREDENTIALS_PSW} | docker login -u ${DOCKER_HUB_CREDENTIALS_USR} --password-stdin"
          // Push image
          sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
        }
      }
    }

  }
}
