pipeline {
    agent any
    
    environment {
        //once you sign up for Docker hub, use that user_id here
        registry = "encoretvm/pythonapp"
        //- update your credentials ID after creating credentials for connecting to Docker Hub
        registryCredential = 'dockerhub_id'
        dockerImage = ''
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/encore-tvm/docker-build-jennkins']]])
            }
        }
        
        stage('Build Docker image') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
                
            }
        }
        
         stage('Upload image in docker hub') {
             steps {
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
             }
             
        }
        
        // Stopping Docker containers for cleaner Docker run
     stage('docker stop container') {
         steps {
            sh 'docker ps -f name=mypythonappContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mypythonappContainer -q | xargs -r docker container rm'
         }
       }
    
    
    // Running Docker container, make sure port 8096 is opened in 
    stage('Docker Run') {
     steps{
         script {
            dockerImage.run("-p 8096:5000 --rm --name mypythonappContainer")
         }
      }
    }
    
    }
}
