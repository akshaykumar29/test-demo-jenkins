def image
pipeline {
    environment {
        registry = "akshaykumar29/moki29"
        registryCredential = 'docker-moka'
        dockerImage = ''
    }
    agent any

    stages{
        
        stage("Build Image") {

            steps {
                script {
                     image = registry + ":${env.BUILD_NUMBER}"
                     println ("${image}")
                     dockerImage = docker.build image
                    
                }
            }

        

        }

        stage("Testing - running in Jenkins Node") {

            steps {
                sh "docker run -d --name ${JOB_NAME} -p 5000:5000 ${image}"
            }
        }
        stage('Cleaning up') {
             steps {
                 sh "docker rm -f ${JOB_NAME}"
               }
        }
        stage("Push to Dockerhub") {
            steps {
                script {
                    docker.withRegistry('', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage("running on prod_server") {
            steps{
                script{
                     def dockerRun = "docker run -d --name ${JOB_NAME} -p 5000:5000 ${image}"
                     sshagent (['prod_server']){
                     sh "ssh -o StrictHostKeyChecking=no ubuntu@13.233.34.41 ${dockerRun}"
                     
                    }
                }
            }
            
        }
          
    }

}
