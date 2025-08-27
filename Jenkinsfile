// groovy pipeline script for a java based app

pipeline {
    agent any

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
                        sh 'docker build -t ankushnagpal10/kube-petclinic-app:3.0.0 .'
                        sh 'docker push ankushnagpal10/kube-petclinic-app:3.0.0'
                    }
                }
            }
        }

    }
}