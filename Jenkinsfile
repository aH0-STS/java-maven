pipeline {
    agent any

    environment {
        IMAGE_NAME = 'harishkoppineni/java-hello-app'
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
            // when {
            //     branch 'develop'
            // }
            steps {
                script {
                    docker.withRegistry('', 'docker-cred') {
                        def image = docker.build("${IMAGE_NAME}:${TAG}")
                        image.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            // when {
            //     branch 'develop'
            // }
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
