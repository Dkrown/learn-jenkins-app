pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'node:18-alpine'
        NETLIFY_SITE_ID = 'e029ffdd-dd03-4484-985a-8802dedc8813'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        // CI_ENVIRONMENT_URL = "${env.STAGING_URL}"
    }
    
    stages {
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
                            image 'mcr.microsoft.com/playwright:v1.50.1-noble'
                            reuseNode true
                        }
                    }
                    environment {
                        CI_ENVIRONMENT_URL = 'STAGING-TO-BE-SET'
                    }
                    steps {
                        sh '''
                            echo "Building production project..."
                            npm ci || { echo "npm ci failed"; exit 1; }
                            npm run build || { echo "npm run build failed"; exit 1; }

                            echo "Deploying to production... Site ID: $NETLIFY_SITE_ID"
                            npm install node-jq --save-dev || { echo "npm install node-jq failed"; exit 1; }
                            npm install netlify-cli || { echo "npm install netlify-cli failed"; exit 1; }
                            node_modules/.bin/netlify --version || { echo "netlify-cli check failed"; exit 1; }
                            node_modules/.bin/netlify status
                            node_modules/.bin/netlify deploy --dir=build --prod
                            CI_ENVIRONMENT_URL=$(node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json)
                            node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json

                            npx playwright test
                            npx playwright --version
                        '''
                    }
                }
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
