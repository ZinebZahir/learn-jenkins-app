pipeline {
    agent any

    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify-token-id')
        NETLIFY_SITE_ID = '1a9896b7-25a0-482f-b358-5b99b31d5851'
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args "-v ${WORKSPACE}:${WORKSPACE}" // Montre le répertoire de travail
                    reuseNode true
                }
            }
            steps {
                dir("${WORKSPACE}") {
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
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args "-v ${WORKSPACE}:${WORKSPACE}" // Montre le répertoire de travail
                    reuseNode true
                }
            }
            steps {
                dir("${WORKSPACE}") {
                    sh '''
                        test -f build/index.html
                        npm test
                    '''
                }
            }
            post {
                always {
                    junit 'src/tests-results/Junit.xml'
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args "-v ${WORKSPACE}:${WORKSPACE}" // Montre le répertoire de travail
                    reuseNode true
                }
            }
            steps {
                dir("${WORKSPACE}") {
                    sh '''
                        npm install netlify-cli
                        node_modules/.bin/netlify --version
                        node_modules/.bin/netlify deploy --prod --dir=build --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN
                    '''
                }
            }
        }
    }
}
