pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                cleanWs()
            }
        }


    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build Image') {
            steps {
                sh 'docker build -t static-website-nginx:develop-${BUILD_ID} .'
            }
        }
        stage('Run Container') {
            steps {
                sh 'docker stop develop-container || true && docker rm develop-container || true'
                sh 'docker run --name develop-container -d -p 8081:80 static-website-nginx:develop-${BUILD_ID}'
            }
        }
        stage('Test Website') {
            steps {
                sh 'curl -I http://54.85.223.42:8081'
            }
        }
        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-auth', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        docker login -u saravana227 -p ${DOCKERHUB_PASSWORD}
                        docker tag static-website-nginx:develop-${BUILD_ID} saravana227
/static-website-nginx:latest
                        docker tag static-website-nginx:develop-${BUILD_ID} saravana227/static-website-nginx:develop-${BUILD_ID}
                        docker push saravana227/static-website-nginx:latest
                        docker push saravana227/static-website-nginx:develop-${BUILD_ID}
                    '''
                }
            }
        }
    }
}
