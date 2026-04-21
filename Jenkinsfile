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

    stage ('update new image in kubernetes yaml files'){
      steps{
        script{ 
          def imageTag = "${DOCKER_IMAGE}:${DOCKER_TAG}"
          sh "sed -i 's|image: .*|image: ${imageTag}|g' K8s_yamls/deployment.yml"
          sh """
          git config user.email "maroof.siddiqui18@gmail.com"
          git config user.name "Maroof49"
          """

          withCredentials([string(credentialsId: 'github-token', variable: 'GIT_TOKEN')]) {
          sh """
          git remote set-url origin https://x-access-token:${GIT_TOKEN}@github.com/maroof49/Production_Test.git
          git add K8s_yamls/deployment.yml
          git commit -m "Update image to ${imageTag}"
          git push origin HEAD:main
          """  }       
          
        }
      }
    }

  }
}
