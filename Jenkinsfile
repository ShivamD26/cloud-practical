pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    stages {
        stage('Build Maven') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/ShivamD26/cloud-practical.git'
                bat 'mvn clean install'
            }
        }
        stage('Build docker image') {
            steps {
                script {
                    bat 'docker build -t shivd26/cloud-prac:latest .'
                }
            }
        }
        stage('Push image to Hub') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'ab1ef886-b666-4d72-ab62-d9c62c80b1aa']) {
                        bat 'docker push shivd26/cloud-prac:latest'
                    }
                }
            }
        }
        stage('Deploy to K8s') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kube-config', variable: 'KUBECONFIG')]) {
                        bat 'kubectl apply -f deploymentservice.yaml'
                    }
                }
            }
        }
    }
    post {
        success {
            emailext (
                subject: "Deployment Success: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: "The deployment was successful!\n\nJob: ${env.JOB_NAME}\nBuild Number: ${env.BUILD_NUMBER}\nBuild URL: ${env.BUILD_URL}",
                to: "youremail@example.com"
            )
        }
        failure {
            emailext (
                subject: "Deployment Failed: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: "The deployment failed. Please check the logs.\n\nJob: ${env.JOB_NAME}\nBuild Number: ${env.BUILD_NUMBER}\nBuild URL: ${env.BUILD_URL}",
                to: "youremail@example.com"
            )
        }
    }
}
