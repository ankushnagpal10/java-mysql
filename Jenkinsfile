// groovy pipeline script for a java based app

pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'ankushnagpal10/kube-petclinic-app:latest'
    }
    stages {
        stage ('Git Checkout') {
            steps{
                git branch: 'main', url: 'https://github.com/ankushnagpal10/java-mysql'
            }
        }

        stage ('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage ('Docker Build and Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker_cred') {
                        sh 'docker build -t ${DOCKER_IMAGE} .'
                        sh 'docker push ${DOCKER_IMAGE}'
                    }
                }
            }
        }

    }
}