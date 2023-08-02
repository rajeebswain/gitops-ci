pipeline{
    agent any
    environment {
        DOCKERHUB_USERNAME = "rajeebswain"
        APP_NAME = "gitops-demo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }
    stages {
        stage('Clean Workspace'){
            steps{
                script{
                    cleanWs ()
                }
            }
        }
        stage('Checkout SCM'){
            steps{
                git credentialsId: 'github', 
                url: 'https://github.com/rajeebswain/gitops.git',
                branch: 'main'
            }
        }
        stage('Build Docker Image'){
            steps{
                script{
                    docker_image = docker.build "${IMAGE_NAME}"
                } 
            }
        }
        stage('Push Docker Image'){
            steps{
                script{
                    docker.withRegistry('', REGISTRY_CREDS){
                        docker_image.push("${BUILD_NUMBER}")
                        docker_image.push('latest')
                    }
                } 
            }
        }
        stage('Delete Docker Image'){
            steps{
               sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
               sh "docker rmi ${IMAGE_NAME}:latest"
            } 
        }
        stage('Trigger config change pipeline'){
            steps {
                   sh"""curl -v -k -u admin:114fea055127cdb3da6e595863f4485ee2 -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' -d 'IMAGE_TAG=${IMAGE_TAG}' 'http://35.90.141.129:8080/job/gitops-deployment/buildWithParameters?token=12345'"""
            }
        }
    }
}       