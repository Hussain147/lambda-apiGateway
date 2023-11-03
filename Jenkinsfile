pipeline {
    environment {
    IMAGE_REPO_NAME = "kubehussain/kasplo-api"
    IMAGE_TAG = "latest"
  }
  agent any

  stages {
    stage('Cloning Git') {
       steps {
         checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/Hussain147/lambda-apiGateway.git']]])     
            }
    }
        
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'docker-creds'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment.yml", kubeconfigId: "kubernetes")
          kubernetesDeploy(configs: "service.yml", kubeconfigId: "kubernetes")
        }
      }
    }
  }
  

}


// ------------------------------------------------

pipeline {
    agent any

    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/sharu1301/kasplo-api.git/']]])     
            }
        }

        stage('Deploy Lambda Function 1') {
            steps {
                script {
                    sh 'aws lambda update-function-code --function-name kasplo-lambda --zip-file fileb://kasplo-lambda-1.zip --region us-east-1'
                }
            }
        }

        stage('Deploy Lambda Function 2') {
            steps {
                script {
                    sh 'aws lambda update-function-code --function-name kasplo-lambda2 --zip-file fileb://kasplo-lambda-2.zip --region us-east-1'
                }
            }
        }
    }
}
