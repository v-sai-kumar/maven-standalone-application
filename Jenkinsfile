pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'gitid' // ID of Docker Hub credentials stored in Jenkins
        DOCKER_IMAGE = 'sai1421/java:latest' // Replace with your Docker Hub username and image name
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/v-sai-kumar/dockerapp.git', branch: 'main'
            }
        }
        
        stage('Build WAR') {
            steps {
                script {
                    // Clean and build the project using Maven
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Ensure the target directory exists
                    sh 'if [ ! -d target ]; then echo "Target directory does not exist"; exit 1; fi'

                    // Create a Docker image
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_HUB_CREDENTIALS) {
                        sh 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    // Stop and remove the old container if it exists
                   //  sh 'docker stop mavenapp || true && docker rm mavenapp || true'
                    // Run the new container
                    sh 'docker run -d --name mavenapp -p 8081:8081 $DOCKER_IMAGE'
                }
            }
        }
    }
     post {
        always {
            cleanWs() // Clean workspace after the build to reduce storage issue
        }
    }
}
