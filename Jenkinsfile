pipeline {
    agent any

    stages {
        stage('without docker'){
            steps{
                sh '''
                     pwd 
                     ls -al
                     touch no-docker.txt
                '''
            }
        }
        
        stage('with docker') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    node --version
                    npm --version
                    #npm ci
                    #npm run build
                    pwd 
                    ls -al
                    touch docker.txt
                '''
            }
        }
    }
}
