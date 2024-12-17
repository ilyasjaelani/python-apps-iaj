pipeline {
    agent any
    environment {
        // Define your Docker Hub credentials and image name here
        DOCKER_IMAGE = 'ilyasjaelani/python-app-iaj:v1' // Image name
        KUBE_CONTEXT = 'iaj-python'  // Kube context if you have multiple clusters
        KUBERNETES_NAMESPACE = 'iaj-python'  // Replace with your namespace
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout your repository
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'git', url: 'git@github.com:ilyasjaelani/python-apps-iaj.git']])
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh '''
                        docker build -t $DOCKER_IMAGE .
                    '''
                }
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'ilyasjae-dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                sh 'docker push $DOCKER_IMAGE'
                }
            }
        }
        // stage('delete manifest in Kubernetes') {
        //     steps {
        //         script {
        //             // Deploy to Kubernetes using kubectl
        //             sh '''
        //                 kubectl delete -f deployment.yaml -n $KUBERNETES_NAMESPACE
        //                 sleep 60
        //             '''
        //         }
        //     }
        // }
        stage('Create namespace on Kubernetes') {
            steps {
                script {
                    // Create namespace on Kubernetes using kubectl
                    sh '''
                        kubectl create namespace $KUBERNETES_NAMESPACE
                    '''
                }
            }
        stage('Deploy again to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using kubectl
                    sh '''
                        kubectl apply -f deployment.yaml -n $KUBERNETES_NAMESPACE
                    '''
                }
            }
        }
        stage('rollout restart  Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using kubectl
                    sh '''
                        kubectl rollout restart deployment/python-app-iaj -n $KUBERNETES_NAMESPACE
                    '''
                }
            }
        }
    }
    post {
        always {
            // Clean up if necessary, for example, remove the Docker image locally
            sh 'docker rmi $DOCKER_IMAGE'
        }
    }
}

