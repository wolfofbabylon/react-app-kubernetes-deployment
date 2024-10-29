pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'tanay598/react-app' // Specify the Docker image name
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-credentials' // Kubernetes credentials ID
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
                    // Build the Docker image
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using kubectl
                    withKubeConfig([credentialsId: KUBECONFIG_CREDENTIALS_ID]) {
                        // Apply the deployment configuration
                        sh "kubectl apply -f my-k8s-deployment.yaml"
                        // Wait for the rollout to complete
                        sh "kubectl rollout status deployment/my-app"
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                // Clean up the Docker image after the build to free up space
                try {
                    docker.image(DOCKER_IMAGE).remove()
                } catch (Exception e) {
                    echo "Failed to remove Docker image: ${e.getMessage()}"
                }
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
