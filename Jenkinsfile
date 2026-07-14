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
                    node -v
                    npm -v
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage ('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Test stage"
                    ls -la
                    node -v
                    npm -v
                    npm ci
                    npm test
                    echo "Looking for index.html file in build directory"
                    grep -R "index.html" build/
                    echo "$?"
                '''
            }
        }
    }
}
