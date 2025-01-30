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
            parallel {
                stage('Uni-Test') {
                    agent {
                        docker {
                            image "${DOCKER_IMAGE}"
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            echo "Running unit tests..."
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
                            reuseNode true
                        }
                    }
                    steps {
                        echo 'E2E Playwright test starting...'
                        sh '''
                            npm install -g serve
                            node_modules/.bin/serve -s build -l 3000 &  # Start server in background
                            SERVER_PID=$!  # Capture process ID

                            until curl --output /dev/null --silent --head --fail http://localhost:3000; do
                                echo 'Waiting for server...'
                                sleep 5
                            done

                            npx playwright test --reporter=html  # Run Playwright tests

                            kill $SERVER_PID  # Stop server after tests complete
                        '''
                    }
                    post {
                        always {
                            junit 'test-results/junit.xml' // Publish the JUnit report
                        }
                    }
                }
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
