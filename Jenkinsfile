pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = credentials('netlify-site-id')
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.0.$BUILD_ID"
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:lts-alpine3.22'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -al
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -al
                '''
            }
        }
        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:lts-alpine3.22'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            test -f build/index.html
                            npm test
                        '''
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        } 
                    }
                }
                stage('E2E tests') {
                    agent {
                        docker {
                            image 'playwright-with-benefits'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Local Report', reportTitles: '', useWrapperFileDirectly: true])
                        } 
                     }
                }
            }
        }
        stage('Deploy staging') {
            agent {
                docker {
                    image 'playwright-with-benefits'
                    reuseNode true
                }
            }
            environment {
                CI_ENVIRONMENT_URL = 'PLACE_HOLDER'
            }
            steps {
                sh '''
                    echo Deploying to STG
                    netlify --version
                    echo "Deploying to staging. Site ID: $NETLIFY_SITE_ID"
                    netlify status
                    netlify deploy --no-build --dir=build --json > stg-deploy-output.json     
                    CI_ENVIRONMENT_URL=$(node-jq -r '.deploy_url' stg-deploy-output.json)
                    echo E2E STG testing
                    npx playwright test --reporter=html
                '''
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Staging E2E Report', reportTitles: '', useWrapperFileDirectly: true])
                } 
            }
        }
        stage('Deploy prod') {
            agent {
                docker {
                    image 'playwright-with-benefits'
                    reuseNode true
                }
            }
             environment {
                CI_ENVIRONMENT_URL = 'https://preeminent-choux-81dd6e.netlify.app'
            }
            steps {
                sh '''
                    echo Deploying to ProdPROD...
                    node --version
                    netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    netlify status
                    netlify deploy --no-build --dir=build --prod
                    echo E2E PROD testing
                    npx playwright test --reporter=html
                '''
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Prod E2E Report', reportTitles: '', useWrapperFileDirectly: true])
                } 
            }
        }
    }
}