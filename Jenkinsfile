// groovy pipeline script for a java based app

pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'ankushnagpal10/kube-petclinic-app'
        CLUSTER_NAME = 'my-kind-cluster'
        COMMIT_ID = "${env.GIT_COMMIT?.take(7)}"

    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ankushnagpal10/java-mysql'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -Dmaven.test.skip=true'
            }
        }

        stage('Docker Build and Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker_cred') {
                        sh 'docker build -t $DOCKER_IMAGE:$COMMIT_ID .'
                        sh ' docker push $DOCKER_IMAGE:$COMMIT_ID'
                    }
                }
            }
        }
        stage('Set up KIND Cluster') {
            steps {
                script {
                    // Ensure KIND and kubectl are installed
                    sh 'kind version'
                    sh 'kubectl version --client'
                    sh 'pwd'
                    // Create or use an existing KIND cluster
                    sh 'kind create cluster --config kind-cluster-config.yaml --name ${CLUSTER_NAME} || true' // Skip if already exists
                }
            }
        }
        stage('Deploy to KIND Cluster') {
            steps {
                script {
                    echo 'Deploying to KIND Cluster...'
                    sh '''
                        kubectl create ns pet-clinic-app || true
                        kubectl create ns pet-clinic-db || true
                        kubectl apply -f secret.yml 
                        '''
                    dir('mysql/') {
                        sh '''
                        kubectl apply -f configmap.yml
                        kubectl apply -f mysql.yml
                        ''' 
                        }
                    dir('java-app') {
                        sh '''
                        sed -i "s|__GIT_COMMIT_ID__|$COMMIT_ID|g" java.yml
                        kubectl apply -f configmap.yml
                        kubectl apply -f java.yml
                        kubectl apply -f https://raw.githubusercontent.com/techiescamp/kubeadm-scripts/main/manifests/metrics-server.yaml
                        kubectl apply -f hpa.yml
			kubectl wait --for=condition=ready pod -l app=java-app --timeout=90s -n pet-clinic-app
                        kubectl port-forward service/java-app-service 9090:9090 -n pet-clinic-app &
                        '''
                    }
                }
            }
        }
    }

}
    
