pipeline {
    agent any
    environment {
        // Define your Docker Hub credentials and image name here
        KUBE_CONTEXT = 'ilyas-wordpress'  // Kube context if you have multiple clusters
        KUBERNETES_NAMESPACE = 'ilyas-wordpress'  // Replace with your namespace
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout your repository
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'git', url: 'git@github.com:ilyasjaelani/python-apps-iaj.git']])
            }
        }
        //stage('Create namespace on Kubernetes') {
        //    steps {
        //        script {
        //           // Create namespace on Kubernetes using kubectl
        //            sh '''
        //                kubectl create namespace $KUBERNETES_NAMESPACE
        //            '''
        //        }
        //    }
        //}
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using kubectl
                    sh '''
                        kubectl apply -k ./ -n $KUBERNETES_NAMESPACE
                        sleep 60
                    '''
                }
            }
        }
        //stage('delete manifest in Kubernetes') {
        //   steps {
        //        script {
        //            // Deploy to Kubernetes using kubectl
        //            sh '''
        //                kubectl delete -k ./ -n $KUBERNETES_NAMESPACE
        //                sleep 60
        //            '''
        //        }
        //    }
        //}
        stage('View Namespaces') {
            steps {
                script {
                    // Create namespace on Kubernetes using kubectl
                    sh '''
                        kubectl get all -n $KUBERNETES_NAMESPACE
                    '''
                }
            }
        }
    }
}

