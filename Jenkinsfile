pipeline {
    agent any
    stages {
        stage('Pull Image') {
            steps {
                sshagent(['docker-server']) {
                    sh 'ssh root@3.82.44.43 "docker pull saravana227/static-website-nginx:latest"'
                }
            }
        }

        stage('Run Container') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                        ssh root@3.82.44.43 "docker stop main-container || true"
                        ssh root@3.82.44.43 "docker rm main-container || true"
                        ssh root@3.82.44.43 "docker run --name main-container -d -p 8082:80 saravana227/static-website-nginx:latest"
                    '''
                }
            }
        }

        stage('Test Website') {
            steps {
                sh 'curl -I http://3.82.44.43:8082 || exit 1'
            }
        }
    }
}
