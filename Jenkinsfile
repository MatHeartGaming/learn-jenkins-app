pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Testing..."
                    test -f build/index.html
                    node --version
                    npm --version
                    npm test
                    ls -la
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.51.1-noble'
                    reuseNode true
                    // args '-u root:root' // Don't do this!!

                }
            }
            steps {
                sh '''
                    npm install serve
                    node_modules/.bin/server -s build &
                    sleep 5
                    npx playwright test --reporter=html
                '''
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}