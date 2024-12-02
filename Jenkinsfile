pipeline { 
    environment { 
        registry = "norelnorel3/docker-image"  // Docker Hub registry
        registryCredential = 'norel-docker-creds'  // Docker Hub credentials ID
        dockerImage = ''  // Placeholder for the Docker image
    }
    agent {
        kubernetes {
            cloud 'nsoc-cicd-127'
            yamlFile './pod.yml'  // Using the default pod template
        }
    }
    stages { 
        stage('Check Docker Daemon') {
            steps {
                container('docker') {
                    sh 'docker version'  // Ensure Docker is running
                }
            }
        }
        stage('Cloning our Git') { 
            steps { 
                git 'https://github.com/norelnorel3/docker-image.git'
            }
        } 
        // stage('Login to Docker Hub') {
        //     steps {
        //         container('docker') {
        //             script {
        //                 // Retrieve Docker Hub credentials and login using --password-stdin
        //                 withCredentials([usernamePassword(credentialsId: registryCredential, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
        //                     sh """
        //                         echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin https://index.docker.io/v1/
        //                     """
        //                 }
        //             }
        //         }
        //     }
        // }
        // stage('Building our image') { 
        //     steps { 
        //         container('docker') {  
        //             script { 
        //                 dockerImage = docker.build registry + ":$BUILD_NUMBER"  // Build Docker image
        //             }
        //         }
        //     } 
        // }

        stage('Deploy our image') { 
            steps { 
                container('docker') {  
                    script { 
                        docker.withRegistry('https://index.docker.io/v1/' , registryCredential )
                        sh """
                            echo Building Docker Image
                        """
                        dockerImage = docker.build registry + ":$BUILD_NUMBER"
                        sh """
                            echo Pushing Docker Image "$dockerImage"
                        """
                        dockerImage.push()  // Push Docker image to Docker Hub
                    }
                }
            }
        } 
        stage('Cleaning up') { 
            steps { 
                container('docker') {  
                    sh "docker rmi $registry:$BUILD_NUMBER"  // Remove local Docker image
                }
            }
        } 
    }
}
