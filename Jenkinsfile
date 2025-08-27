// groovy pipeline script for a java based app

pipeline {
    agent any

    stages {
        stage ('Git Checkout') {
            steps{
                git branch: 'main', url: 'https://github.com/ankushnagpal10/java-mysql'
            }
        }
    }
}