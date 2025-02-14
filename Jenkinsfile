pipeline {
    agent any
//This pipeline is used to build a nodejs application
/*
    It uses npm ci
    npm run build
    np test 
    A container can be run as root user but that is never recommended as it may permission issues later on --> args '-u root:root'
*/
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
                    test -f build/index.html
                    npm test
                '''
            }
        }
            stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }

            steps {
                /*Here we are installing the web server and running it. & symbol is used to run the server in background. If we 
                dont use & pipeline will stuck forever. We need to put a sleep for few seconds to get the web server started.
                 Report will be published in HTML format. This can be seen under workspace and can be accessed through URL as well.
                 We would need to add HTML Publisher plugin for this.
                 Additionally run below script in Jenkins Dashboard > Manage Jenkins > section “Tools and actions” > Script Console:

                System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "sandbox allow-scripts;")

                 */
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test --reporter=html
                '''
            }
        }

    }

    post {
        always {
            junit 'jest-results/junit.xml'
            //To get this Goto --> Pipeline Syntax --> Select Publish HTML report --> At the bottom Generate the code
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: 'Playwright Report', useWrapperFileDirectly: true])
        }
    }
}
