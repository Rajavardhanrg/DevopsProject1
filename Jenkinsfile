pipeline {
    agent any
    
    stages {
        stage ("Code") {
            steps {
                echo " Cloning the Code"
                git branch: 'main', url: 'https://github.com/Rajavardhanrg/DevopsProject1.git' 
            }
        }
        stage(" Maven Unit Test") {  
            steps{
                  sh 'mvn test'        
            }
        }
        stage(" Maven Build") {
            steps{
                  sh 'mvn clean install'   
            }
        }
        stage("5. Maven Integration Test") {
            steps{
                  sh 'mvn verify'          
            }
        }
        stage ("Build"){
            steps {
                echo "Build the image"
                sh "docker build -t ci-cd-pipeline ."
            }
        }
        stage ("Pushing the image to Dockerhub"){
            steps {
                echo "pushing image to docker hub"
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]) {
                sh "docker tag ci-cd-pipeline ${env.dockerHubUser}/ci-cd-pipeline:latest"
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                sh "docker push ${env.dockerHubUser}/ci-cd-pipeline:latest"
                }
            }
        }
         stage('Deploy to Kubernets'){
             steps{
                 script{
                     {
                         withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubernetes', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                         sh 'kubectl delete --all pods'
                         sh 'kubectl apply -f deployment.yaml'
                         sh 'kubectl apply -f service.yaml'
                         }
                     }
                 }
             }
         }
    }
}
