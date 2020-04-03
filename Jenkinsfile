pipeline {
   agent any
   environment {
       registry = "2017330017/k8scicd"
       GOCACHE = "/tmp"
   }
   stages {
       stage('Build') {
           agent {
               docker {
                   image 'golang:latest'
               }
           }
           steps {
               // Create our project directory.
               sh 'cd ${GOPATH}/src'
               sh 'mkdir -p ${GOPATH}/src/hello-world'
               // Copy all files in our Jenkins workspace to our project directory.               
               sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
               // Build the app.
               sh 'go build'              
           }    
       }
       stage('Test') {
           agent {
               docker {
                   image 'golang'
               }
           }
           steps {                
               // Create our project directory.
               sh 'cd ${GOPATH}/src'
               sh 'mkdir -p ${GOPATH}/src/hello-world'
               // Copy all files in our Jenkins workspace to our project directory.               
               sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
               // Remove cached test results.
               sh 'go clean -cache'
               // Run Unit Tests.
            //    sh 'go test ./... -v -short'           
           }
       }
       stage('Publish') {
           environment {
               registryCredential = 'dockerhub'
           }
           steps{
               script {
                   def appimage = docker.build registry + ":$BUILD_NUMBER"
                   docker.withRegistry( '', registryCredential ) {
                       appimage.push()
                       appimage.push('latest')
                   }
               }
           }
       }
       stage ('Deploy') {
           steps {
               script{
                   sh "chmod +x changeTag.sh"
                   sh "./changeTag.sh ${BUILD_NUMBER}"
                   sh "kubectl create -f deployment1.yml"
                   sh "kubectl create -f service.yml"
                //    try{
                //    }catch(error){
                //         sh "kubectl create -f deployment.yml"
                //         sh "kubectl create -f service.yml"
                //         }
               }
            //    script{
            //        def image_id = registry + ":$BUILD_NUMBER"
            //        sh "ansible-playbook  playbook.yml --extra-vars \"image_id=${image_id}\" -vvv"
            //    }
           }
       }
   }
}
