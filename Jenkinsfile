pipeline {
  environment {
    dockerimagename = "bravinwasike/react-app"
    dockerImage = ""
  }

  agent any

  tools {
    docker 'myDocker' // Configure Docker tool globally
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
          dockerImage = docker.build(dockerimagename)
        }
      }
    }

    stage('Push Image') {
      environment {
        registryCredential = 'dockerhub-credentials'
      }
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
            dockerImage.push('latest')
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(kubeconfigId: 'kubeconfig-credentials', configs: ["deployment.yaml", "service.yaml"])
        }
      }
    }
  }

  post {
    always {
      script {
        dockerImage?.with {
          println("Cleaning up Docker image")
          it.remove()
        }
      }
      cleanWs() // Clean workspace after build
    }
  }
}

