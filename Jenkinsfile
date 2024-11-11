pipeline {
    agent any

    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify-token-id') // Jenkins credential ID for Netlify token
        NETLIFY_SITE_ID = 'fe11aa4e-981c-4d1d-8366-e58ab69e7394' // Replace with your actual Netlify Site ID
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-v /C/ProgramData/Jenkins/.jenkins/workspace/CI_pipeline:/workspace'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    cd /workspace
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
                    args '-v /C/ProgramData/Jenkins/.jenkins/workspace/CI_pipeline:/workspace'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    cd /workspace
                    test -f build/index.html
                    npm test
                '''
            }
            post {
                always {
                    junit '/workspace/src/tests-results/Junit.xml'
                }
            }
        }

        stage('Deploy') {
    agent {
        docker {
            image 'node:18-alpine'
            args '-v /C/ProgramData/Jenkins/.jenkins/workspace/CI_pipeline:/workspace'
            reuseNode true
        }
    }
    steps {
        withCredentials([string(credentialsId: 'netlify-token-id', variable: 'NETLIFY_AUTH_TOKEN')]) {
            sh '''
                cd /workspace
                npm install netlify-cli
                node_modules/.bin/netlify --version
                node_modules/.bin/netlify deploy --prod --dir=build --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN
            '''
        }
    }
}
  
    }
}
