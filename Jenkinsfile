pipeline {
  environment {
    dockerimagename = "bravinwasike/react-app"
    dockerImage = ""
  }

  agent any

  tools {
    dockerTool 'myDocker' // Use dockerTool to configure Docker
  }

  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/wolfofbabylon/react-app-kubernetes-deployment.git'
      }
    }

    stage('Build Image') {
      steps {
        script {
          // Build the Docker image
          dockerImage = docker.build(dockerimagename)
        }
      }
    }

    stage('Push Image') {
      environment {
        registryCredential = 'dockerhub-credentials' // Make sure this is correct
      }
      steps {
        script {
          // Push the image to Docker Hub
          docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
            dockerImage.push('latest') // This pushes the image tagged with 'latest'
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        script {
          // Deploy to Kubernetes using the specified kubeconfig
          kubernetesDeploy(kubeconfigId: 'kubeconfig-credentials', configs: ["deployment.yaml", "service.yaml"])
        }
      }
    }
  }

  post {
    always {
      script {
        // Clean up the Docker image
        dockerImage?.with {
          println("Cleaning up Docker image")
          it.remove()
        }
      }
      cleanWs() // Clean workspace after build
    }
  }
}
