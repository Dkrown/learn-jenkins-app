pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'node:18-alpine'
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
        */
        stage('Test') {
            agent {
                docker {
                    image "${DOCKER_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Running tests..."
                    npm test
                '''
            }
            post {
                always {
                    junit 'test-results/junit.xml' // Publish the JUnit report
                }
            }
        }

        stage('E2E'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode
                }
            }
            steps {
                echo 'E2E playwright test starting...'
                sh '''
                    npm install -g serve
                    serve -s build
                    npx playwright test
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
