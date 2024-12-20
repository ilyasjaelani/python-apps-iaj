pipeline {
    agent any
    environment {
        // Define your Docker Hub credentials and image name here
        KUBE_CONTEXT = 'ilyas-wordpress'  // Kube context if you have multiple clusters
        KUBERNETES_NAMESPACE = 'ilyas-wordpress'  // Replace with your namespace
    }
    stages {
        //stage('Checkout') {
        //    steps {
        //        // Checkout your repository
        //        checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'git', url: 'git@github.com:ilyasjaelani/wp-iaj.git']])
        //    }
        //}
        stage('View Namespaces') {
            steps {
                script {
                    // Create namespace on Kubernetes using kubectl
                    sh '''
                        kubectl get all -n  $KUBERNETES_NAMESPACE
                    '''
                }
            }
        }
        stage('View describe pods wp') {
            steps {
                script {
                    // Create namespace on Kubernetes using kubectl
                    sh '''
                        kubectl describe pods ilyas-wordpress-797bd4568-npvc5 -n $KUBERNETES_NAMESPACE
                    '''
                }
            }
        }
        stage('View describe pods mysql') {
            steps {
                script {
                    // Create namespace on Kubernetes using kubectl
                    sh '''
                        kubectl describe pods ilyas-wordpress-mysql-fb58f47d-qpz9b -n $KUBERNETES_NAMESPACE
                    '''
                }
            }
        }
        stage('View Nodes') {
            steps {
                script {
                    // Create namespace on Kubernetes using kubectl
                    sh '''
                        kubectl describe nodes
                    '''
                }
            }
        }
        stage('View Nodes top') {
            steps {
                script {
                    // Create namespace on Kubernetes using kubectl
                    sh '''
                        kubectl top node
                    '''
                }
            }
        }
    }
}

