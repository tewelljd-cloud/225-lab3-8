pipeline {
    agent any

    environment {
        // Jenkins credential for Docker Hub
        DOCKER_CREDENTIALS_ID = 'roseaw-dockerhub'

        // Your GitHub fork
        GITHUB_URL = 'https://github.com/tewelljd-cloud/225-lab3-8.git'

        // Kubernetes kubeconfig credential for your course cluster
        KUBECONFIG = credentials('tewelljd-fl26')
    }

    stages {

        // Stage 1: Check out your GitHub repository
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: "${GITHUB_URL}"
                    ]]
                ])
            }
        }

        // Stage 2: Deploy MongoDB and Mongo Express
        stage('Build Mongo Stateful Set') {
            steps {
                script {
                    sh 'kubectl apply -f mongo.yaml'
                    sh 'kubectl apply -f mongo-configmap.yaml'
                    sh 'kubectl apply -f mongo-express.yaml'
                }
            }
        }

        // Stage 3: Verify Kubernetes resources
        stage('Check Kubernetes Cluster') {
            steps {
                script {
                    sh 'kubectl get all'
                    sh 'kubectl get services'
                    sh 'kubectl get statefulsets'
                }
            }
        }
    }

    // Build notifications
    post {
        success {
            slackSend(
                color: 'good',
                message: "Build Completed Successfully: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }

        unstable {
            slackSend(
                color: 'warning',
                message: "Build Unstable: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }

        failure {
            slackSend(
                color: 'danger',
                message: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
    }
}
