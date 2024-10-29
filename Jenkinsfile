pipeline {
  environment {
    dockerimagename = "tanay598/react-app" // Docker image name
    dockerImage = ""
  }

  agent any

  tools {
    dockerTool 'myDocker' // Configures Docker with 'myDocker' tool
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
        registryCredential = 'dockerhub-credentials' // Docker Hub credentials
      }
      steps {
        script {
          // Log in to Docker Hub and push the image
          docker.withRegistry('https://index.docker.io/v1/', registryCredential) {
            dockerImage.push('latest') // Push the image tagged with 'latest'
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
        if (dockerImage) {
          println("Cleaning up Docker image")
          docker.image(dockerimagename).remove() // Removes the Docker image by its name
        }
      }
      cleanWs() // Clean workspace after build
    }
  }
}
