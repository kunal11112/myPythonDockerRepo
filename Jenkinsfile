pipeline {
    agent any
    environment {
        registry = "kunal1996/pythonapp" // Corrected the image name
    }
    stages {
        stage('checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/kunal11112/myPythonDockerRepo']]])
            }
        }
        
        stage('build image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}:${env.BUILD_NUMBER}")
                }
            }
        }
        
        stage('upload dockerhub') {
            environment {
                registryCredential = 'dockerhub'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) { registry ->
                        dockerImage.push("latest")
                    }
                }
            }
        }
        
        stage('K8S Deploy') {
            steps {   
                 withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'k8s', contextName: '', credentialsId: 'SECRET_TOKEN', namespace: 'default', serverUrl: 'https://3.93.73.131:6443']]) {
                 sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'  
                 sh 'chmod u+x ./kubectl'  
                 sh './kubectl get nodes'
                 sh './kubectl apply -f  k8s-deployment.yaml -n springboot-app-ns'
                }
            }
        }
    }    
}
