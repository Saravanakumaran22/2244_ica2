pipeline {
    agent any
    stages {
        stage('Pull Image') {
            steps {
                // Pulls the latest image from Docker Hub on the remote server
                sshagent(['docker-server']) {
                    sh 'ssh root@44.223.75.239 "docker pull saravana227/static-website-nginx:latest"'
                }
            }
        }

        stage('Run Container') {
            steps {
                // Stops and removes any existing container, then runs a new one on the remote server
                sshagent(['docker-server']) {
                    sh '''
                        ssh root@44.223.75.239 "docker stop main-container || true"
                        ssh root@44.223.75.239 "docker rm main-container || true"
                        ssh root@44.223.75.239 "docker run --name main-container -d -p 8082:80 saravana227/static-website-nginx:latest"
                    '''
                }
            }
        }

        stage('Test Website') {
            steps {
                // Tests if the website is accessible on the new container on the remote server
                sh 'curl -I http://44.223.75.239:8082 || exit 1'
            }
        }
    }
}
