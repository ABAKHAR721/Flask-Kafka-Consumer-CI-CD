pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'abakhar217/service-b:latest'
        K8S_DEPLOYMENT = 'service-b-deployment.yaml'
        K8S_SERVICE = 'service-b-service.yaml'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }
         stage('Push to Docker Registry') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerpassword', usernameVariable: 'dockeruser')]) {
                    sh "docker login -u $dockeruser -p $dockerpassword "
                    sh 'docker push $DOCKER_IMAGE'
                }
                    
                }
            }
         }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                     kubeconfig(credentialsId: 'kubeconfig', serverUrl: '') {
                            sh "kubectl apply -f k8s/$K8S_DEPLOYMENT"
                            sh "kubectl apply -f k8s/$K8S_SERVICE"
                        }
                }
            }
        }
    }
    post {
        always {
            cleanWs() // Clean workspace after each build
        }
    }
}
