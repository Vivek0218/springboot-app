pipeline {
    agent any
    
    tools {
        maven "maven 3.8.6"
    }
    
    environment {
        registryName = "testacr0101"
        registryCredentials = "TestACR01_Credentials"
        registryUrl = "testacr0101.azurecr.io"
        dockerImage = ""
    }
    
    stages {
        
        stage('SCM Checkout'){
            steps{
            checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'GitHubCredendtials', url: 'https://github.com/Vivek0218/springboot-app.git']]])
                }
        }
        
        stage('Build Springboot.jar file'){
            steps{
                sh "mvn clean install"
                
            }
        }
        
        stage('Build Docker Image'){
            steps {
                script {
                  dockerImage = docker.build registryName  
                }
            }
            
        }
        
        stage('Push DockerImage to ACR'){
            steps {
                script {
                    docker.withRegistry( "http://${registryUrl}", registryCredentials) {
                    dockerImage.push()
                    }
                }
            }
        }
        
        stage('Deploy to AKS'){
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'TestAKS0101-Kubeconfig', namespace: '', serverUrl: '') {
                sh "kubectl apply -f jenkins-aks-deploy-from-acr.yaml"
                }
            }
        }
    }
}
