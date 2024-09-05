pipeline { 
    environment { 
        registry = "norelnorel3/docker-image"  // Docker Hub registry
        registryCredential = 'cef210e3-afac-4305-9696-f43875336e5a'  // Docker Hub credentials ID
        dockerImage = ''  // Placeholder for the Docker image
    }
    agent {
        kubernetes {
            label 'kubernetes'
            inheritFrom 'default'  // Using the default pod template
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
        stage('Login to Docker Hub') {
            steps {
                container('docker') {
                    script {
                        // Retrieve Docker Hub credentials and login using --password-stdin
                        withCredentials([usernamePassword(credentialsId: registryCredential, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                            sh """
                                echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin https://index.docker.io/v1/
                            """
                        }
                    }
                }
            }
        }
        stage('Building our image') { 
            steps { 
                container('docker') {  
                    script { 
                        dockerImage = docker.build registry + ":$BUILD_NUMBER"  // Build Docker image
                    }
                }
            } 
        }
        stage('Deploy our image') { 
            steps { 
                container('docker') {  
                    script { 
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
