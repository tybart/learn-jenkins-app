pipeline {
    agent any

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
        stage('Test') {
            steps {
                sh '''
                    echo 'Test stage' 
                    test -f build/index.html
                    npm test
                '''
            }
        }
    }
}