pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '84aecc75-7971-48aa-b0e9-b4c5dfcbdb36'
        NETLIFY_AUTH_TOKEN = credentials('netifly-token')
    }

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

        stage('Run Tests') {
            parallel {
                stage('Unit Test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            echo "Testing..."
                            # +test -f build/index.html
                            node --version
                            npm --version
                            npm test
                            ls -la
                        '''
                    }

                    post {
                        always {
                            junit 'test-results/junit.xml'
                        }
                    }
                }

                /*stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.50.0-noble'
                            reuseNode true
                            // args '-u root:root' // Don't do this!!

                        }
                    }
                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/server -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }

                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }*/
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }

    }
}