pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'my-docker-image:latest'
    }
    stages {
        stage('Checkout') {
            steps {
                // Check out the code from the source control
                checkout scm
            }
        }
        stage('Build') {
            steps {
                script {
                    // Build Docker image
                    docker.build(DOCKER_IMAGE)
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Ensure that the Kubernetes plugin or CLI tool is used
                    // Here we use kubectl directly for deployment instead of kubernetesDeploy
                    withKubeConfig([credentialsId: 'kubeconfig-credentials']) {
                        sh """
                            kubectl apply -f my-k8s-deployment.yaml
                            kubectl rollout status deployment/my-app
                        """
                    }
                }
            }
        }
    }
    post {
        always {
            script {
                // Clean up the Docker image after the build
                docker.image(DOCKER_IMAGE).remove()
            }
        }
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
