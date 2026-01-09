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
                withCredentials([usernamePassword(
                    credentialsId: 'dockerHub',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                 sh '''
                     docker tag ci-cd-pipeline $DOCKER_USERNAME/ci-cd-pipeline:latest
                     docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                     docker push $DOCKER_USERNAME/ci-cd-pipeline:latest
                     '''
                }
            }
        }
         stage('Deploy to Kubernets'){
             steps{
                 script{
                     withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubernetes', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                          sh '''
                          kubectl delete --all pods
                          kubectl apply -f deployment.yaml
                          kubectl apply -f service.yaml
                          '''
                          }
                 }
             }
         }
    }
}
