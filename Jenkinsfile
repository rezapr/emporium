pipeline {
    agent any
    tools {
        nodejs 'angular'
    }
    //agent {
        //dockerContainer { image 'node:24-alpine' }
    //}  
    environment {
        BUILD_DIR = "dist/app"  // Output folder after Angular build
        DEPLOY_DIR = "/var/lib/jenkins/workspace/emporium-Frontend/emporium" // Target directory for deployment
    }
    stages {
        stage('Clone Repository') {
            steps {
                git(url: 'https://github.com/rezapr/emporium.git', branch: 'main')
            }
        }
        stage('Verify Node.js and npm') {
            steps {
                sh 'node -v'
                sh 'npm -v'
                sh 'ng version'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm ci'  // More reliable than 'npm install' for CI/CD
            }
        }
        stage('Build Angular App') {
            steps {
                sh 'ng build --configuration=production'
            }
        }
        stage('Deploy') {
            steps {
                script {
                    if (fileExists(BUILD_DIR)) {
                        echo "🚀 Deploying build to ${DEPLOY_DIR}..."
                        // Ignore errors if DEPLOY_DIR does not exist
                        sh "rm -rf ${DEPLOY_DIR} || true"
                
                        // Create the deployment directory
                        sh "mkdir -p ${DEPLOY_DIR}"
                
                        // Copy build artifacts
                        sh "cp -r ${BUILD_DIR}/* ${DEPLOY_DIR}/"
                        echo "✅ Deployment complete."
                   } else {
                        error "❌ Build directory not found: ${BUILD_DIR}"
                    }
                }
            }
        }
    }
    post {
        success {
            echo '✅ Build and Deployment Successful!'
        }
        failure {
            echo '❌ Build Failed!'
        }
    }
}
