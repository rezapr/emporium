pipeline {
    agent none

    environment {
        DOCKER_IMAGE = 'renderman/emporium'
        DOCKER_TAG   = "${BUILD_NUMBER}"
    }

    stages {

        stage('Build Angular') {
            agent {
                docker {
                    image 'node:24-alpine'
                    args '-u root'
                }
            }
            steps {
                sh '''
                  node -v
                  npm -v
                  npm install -g @angular/cli
                  npm install
                  ng build --configuration=production
                '''
            }
        }

        stage('Build & Push Docker Image') {
            agent { label 'docker' }   // ⬅ node Jenkins yg punya docker
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-rezapr',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      docker version
                      docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                    '''
                }
            }
        }
    }
}
