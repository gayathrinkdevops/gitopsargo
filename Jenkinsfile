pipeline {
    agent any
    environment {
        DOCKERHUB_USERNAME = "gayathrink91"
        APP_NAME = "gitops-demo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }

    stages {
       stage('Cleanup Workspace'){
          steps {
            script {
                cleanWs()
            }
           
          }
        }

        stage('Checkout SCM'){
           steps {
                git credentialsId: 'github',
                url: 'https://github.com/gayathrinkdevops/gitopsargo.git',
                branch: 'master'
            } 
       }
       stage('Build Docker Image'){
          steps {
              script{
                  docker_image = docker.build "${IMAGE_NAME}"
               }
           }
       }  
       stage('Push Docker Image'){
            steps {
              script{
                  docker.withRegistry('', REGISTRY_CREDS){
                    docker_image.push("${BUILD_NUMBER}")
                    docker_image.push('latest')

                  }
               }
           }
       }
       stage('Delete Docker Images'){
          steps {
            sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
            sh "docker rmi ${IMAGE_NAME}:latest"  
        }

       }
       stage('Updating kubernetes deployment file'){
          steps {
            sh "cat deployment.yml"
            sh "sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml"  
            sh "cat deployment.yml"
           }
       }
       stage('Push the changed deployment file to Git'){
           steps {
              script{
                  sh """
                  git config --global user.name "gayathri"
                  git config --global user.email "gayathrink91@gmail.com"
                  git add deployment.yml
                  git commit -m 'Update the deployment file' """
                  withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'pass', usernameVariable: 'user')]) {
                      sh "git push http://$user:$pass@github.com/gayathrinkdevops/gitopsargo.git master"
                   }
               }
           }
       }
    }
}                 