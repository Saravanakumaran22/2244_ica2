pipeline {
    agent any
    stages {
        stage('Pull Image') {
            steps {
                // Pulls the latest image from Docker Hub
                sh 'docker pull $USERNAME/static-website-nginx:latest'
            }
        }

        stage('Run Container') {
            steps {
                // Stops and removes any existing container, then runs a new one
                sh 'docker stop main-container || true && docker rm main-container || true'
                sh 'docker run --name main-container -d -p 8082:80 $USERNAME/static-website-nginx:latest'
            }
        }

        stage('Test Website') {
            steps {
                // Tests if the website is accessible on the new container
                sh 'curl -I http://54.85.223.42:8082'
            }
        }
    }
}
