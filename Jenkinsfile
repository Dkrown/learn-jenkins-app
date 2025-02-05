pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'node:18-alpine'
        /*NETLIFY_SITE_ID = 'e029ffdd-dd03-4484-985a-8802dedc8813'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        CI_ENVIRONMENT_URL = "${env.STAGING_URL}"*/
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

        stage('E2E') {
                    agent {
                        docker {
                            /*image 'mcr.microsoft.com/playerwright:v1.39.0-jammy'*/
                            image 'mcr.microsoft.com/playwright:v1.50.0-noble'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test
                            npx playwright --version
                            npx playwright show-report
                        '''
                    }
                }

        /*stage('Deploy') {
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

        stage('Deploy staging!') {
            agent {
                docker {
                    image "${DOCKER_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Building staging project..."
                    npm ci || { echo "npm ci failed"; exit 1; }
                    npm run build || { echo "npm run build failed"; exit 1; }

                    echo "Deploying to staging... Site ID: $NETLIFY_SITE_ID"
                    npm install netlify-cli node-jq || { echo "npm install netlify-cli failed"; exit 1; }
                    node_modules/.bin/netlify --version || { echo "netlify-cli check failed"; exit 1; }
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                    node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json                
                '''
                script {
                    env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
                }
            }
            
        }

        stage('Approval') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    input message: 'Caution, deploy to production?', ok: 'Yes, proceed to production.'
                    
                    }
                
            }
        }

        stage('Deploy production.') {
            agent {
                docker {
                    image "${DOCKER_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Building production project..."
                    npm ci || { echo "npm ci failed"; exit 1; }
                    npm run build || { echo "npm run build failed"; exit 1; }

                    echo "Deploying to production... Site ID: $NETLIFY_SITE_ID"
                    npm install netlify-cli || { echo "npm install netlify-cli failed"; exit 1; }
                    node_modules/.bin/netlify --version || { echo "netlify-cli check failed"; exit 1; }
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                
                '''
            }
        } */
    }

    post {
        always {
            junit 'jest-results/junit.xml'
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
