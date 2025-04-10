pipeline {
    agent any

    environment {
        IMAGE_NAME = 'your-dockerhub-username/java-hello-app'
        TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build and Push') {
            when {
                branch 'develop'
            }
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-creds') {
                        def image = docker.build("${IMAGE_NAME}:${TAG}")
                        image.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch 'develop'
            }
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-cred-id', variable: 'KUBECONFIG')]) {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
}
