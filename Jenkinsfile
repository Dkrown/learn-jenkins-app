pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'node:18-alpine'
        NETLIFY_SITE_ID = 'e029ffdd-dd03-4484-985a-8802dedc8813'
    }
    
    stages {
    /*    stage('Build') {
            agent {
                docker {
                    image "${DOCKER_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Building project..."
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image "${DOCKER_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Deploying project... Site ID: $NETLIFY_SITE_ID"
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                '''
            }
        }*/
        stage('Build and Deploy') {
            agent {
                docker {
                    image "${DOCKER_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Building project..."
                    npm ci || { echo "npm ci failed"; exit 1; }
                    npm run build || { echo "npm run build failed"; exit 1; }

                    echo "Deploying project... Site ID: $NETLIFY_SITE_ID"
                    npm install netlify-cli || { echo "npm install netlify-cli failed"; exit 1; }
                    node_modules/.bin/netlify --version || { echo "netlify-cli check failed"; exit 1; }
                '''
            }
        }        
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            echo 'Pipeline succeeded.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
