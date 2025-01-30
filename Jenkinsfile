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

        stage('Tests') {
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
                            echo 'Installing serve locally...'
                            npm install serve
                            npx serve -s build -l 3000 &  # Start server in background
                            
                            #SERVER_PID=$!  # Capture process ID
                            
                            # Ensure the server process is alwasy killed when the script exits
                            # trap 'kill $SERVER_PID' EXIT

                            # Wait until the server is accessible
                            for i in {1..10}; do
                                curl --output /dev/null --silent --head --fail http://localhost:3000 && break
                                echo 'Waiting for server...'
                                sleep 2
                            done

                            echo 'Running Playwright tests...
                            npx playwright test --reporter=html  # Run Playwright tests

                            echo 'E2E tests completed successfully.'
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
