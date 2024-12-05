pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm 
            }
        }

        stage('Copy Files to Remote Server') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                    scp -r Dockerfile Jenkinsfile README.md assets error images index.html root@52.87.247.202:/opt/2244_ica2/
                    '''
                }
            }
        }

        stage('Build Image') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                    ssh root@52.87.247.202 "cd /opt/2244_ica2 && docker build -t static-website-nginx:develop-${BUILD_ID} ."
                    '''
                }
            }
        }

        stage('Run Container') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                    ssh root@52.87.247.202 "docker stop develop-container || true && docker rm develop-container || true && docker run --name develop-container -d -p 8081:80 static-website-nginx:develop-${BUILD_ID}"
                    '''
                }
            }
        }

        stage('Test Website') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                    ssh root@52.87.247.202 "curl -I http://52.87.247.202:8081"
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sshagent(['docker-server']) {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-auth', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''
                        ssh root@52.87.247.202 "docker login -u $USERNAME -p $PASSWORD"
                        ssh root@52.87.247.202 "docker tag static-website-nginx:develop-${BUILD_ID} $USERNAME/static-website-nginx:develop-${BUILD_ID}"
                        ssh root@52.87.247.202 "docker push $USERNAME/static-website-nginx:develop-${BUILD_ID}"
                        '''
                    }
                }
            }
        }
    }
}
