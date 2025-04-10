pipeline {
    agent any

    environment {
        IMAGE_NAME = 'saiyash000/java-hello-app'
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
                sh 'ls -l target'

            }
        }

        stage('Docker Build and Push') {
            when {
                branch 'Develop'
            }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials1') {
                        def image = docker.build("${IMAGE_NAME}:${TAG}")
                        image.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch 'Develop'
            }
            steps {
                //withCredentials([file(credentialsId: 'kubeconfig-cred-id', variable: 'KUBECONFIG')])
                 withKubeConfig([credentialsId: 'kubeconfig'])
                {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
}
