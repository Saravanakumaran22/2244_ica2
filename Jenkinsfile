pipeline {
    agent any
    stages {
        stage('Pull Image') {
            steps {
                sshagent(['docker-server']) {
                    sh 'ssh root@52.87.247.202 "docker pull saravana227/static-website-nginx:latest"'
                }
            }
        }

        stage('Run Container') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                        ssh root@52.87.247.202 "docker stop main-container || true"
                        ssh root@52.87.247.202 "docker rm main-container || true"
                        ssh root@52.87.247.202 "docker run --name main-container -d -p 8082:80 saravana227/static-website-nginx:latest"
                    '''
                }
            }
        }

        stage('Test Website') {
            steps {
                sh 'curl -I http://52.87.247.202:8082 || exit 1'
            }
        }
    }
}
