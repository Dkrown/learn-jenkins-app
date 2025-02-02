pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'node:18-alpine'
        NETLIFY_SITE_ID = 'e029ffdd-dd03-4484-985a-8802dedc8813'
    }
    
    stages {
        stage('Build') {
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
