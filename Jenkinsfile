pipeline {
    agent any

    environment {
        registry = "kunal1996/pyhtonapp/"
    }
    stages {
        stage('checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/akannan1087/myPythonDockerRepo']]])
            }
        }
        
        stage ("build image") 
        {
            steps {
                script {
                    dockerImage = docker.build registry
                      dockerImage.tag("$BUILD_NUMBER")
                    }
                }
        }
        
        stage ("upload dockerhub") {
            steps {
                script {
                sh  'docker login --username kunal1996 --password-stdin dockerhub"
                sh 'docker push kunal1996/pyhtonapp/:$BUILD_NUMBER'
                }
            }
        }
        
    // Avoid latest tag image and pass build ID dynamically from Jenkins pipeline
       stage('K8S Deploy') {
        steps{   
            script {
                withKubeConfig([credentialsId: 'K8S', serverUrl: '']) {
                echo "Current build number is: ${env.BUILD_ID}"
               // Replace the placeholders in the deployment.yaml file 
                sh """ 
                sed -i 's/\${BUILD_NUMBER}/${env.BUILD_ID}/g' k8s-deployment.yaml
                """ 
                sh ('kubectl apply -f  k8s-deployment.yaml -n springboot-app-ns')
                }
            }
        }
       }
    }    
}
