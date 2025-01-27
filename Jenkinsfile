pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'node:18-alpine' // Define reusable Docker image
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
                    echo "Running Build Stage..."
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }
        stage('Test') {
            agent {
                docker {
                    image "${DOCKER_IMAGE}" // Use the same Docker image for testing
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Running Test Stage..."
                    test -f build/index.html
                    npm test
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
