pipeline {
  agent any
  environment {
    DOCKER_IMAGE = 'library/python-app-iaj:v1' // Image name
    KUBE_CONTEXT = 'iaj-python'  // Kube context if you have multiple clusters
    KUBERNETES_NAMESPACE = 'iaj-python'  // Replace with your namespace
    REGISTRY = 'registry.lmd.co.id' // Registry
  }
  stages {
    stage('Checkout') {
      steps {
        // Checkout your repository
        checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'ilyas-github', url: 'git@github.com:ilyasjaelani/python-apps-iaj.git']])
      }
    }
    stage('Build Docker Image') {
      steps {
        script {
          // Build Docker image
          sh '''
            docker build -t $REGISTRY/$DOCKER_IMAGE .
          '''
        }
      }
    }
    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'ilyas-harbor', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh '''
            docker login -u $dockerHubUser -p $dockerHubPassword https://$REGISTRY
            docker push $REGISTRY/$DOCKER_IMAGE
          '''
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'ilyas-k3s', contextName: '', credentialsId: 'ilyas-k3s', namespace: 'default', serverUrl: 'https://172.16.25.129:6443']]) {
          sh '''
            curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"
            chmod u+x ./kubectl
            ./kubectl apply -f deployment.yaml -n $KUBERNETES_NAMESPACE
            sleep 60
          '''
        }
      }
    }
    stage('Rollout Restart Kubernetes') {
      steps {
        withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'ilyas-k3s', contextName: '', credentialsId: 'ilyas-k3s', namespace: 'default', serverUrl: 'https://172.16.25.129:6443']]) {
          sh '''
            ./kubectl rollout restart deployment/python-app-iaj -n $KUBERNETES_NAMESPACE
          '''
        }
      }
    }
    stage('View Namespaces') {
      steps {
        withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'ilyas-k3s', contextName: '', credentialsId: 'ilyas-k3s', namespace: 'default', serverUrl: 'https://172.16.25.129:6443']]) {
          sh '''
            ./kubectl get all -n $KUBERNETES_NAMESPACE
          '''
        }
      }
    }
  }
  post {
    always {
      // Clean up if necessary, for example, remove the Docker image locally
      sh 'docker rmi $REGISTRY/$DOCKER_IMAGE'
    }
  }
}

