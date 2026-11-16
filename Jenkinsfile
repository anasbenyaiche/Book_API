pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    environment {
        SCANNER_HOME = tool 'SonarQubeScanner'
    }

    stages {
        stage('Install (via Nexus)') {
            steps {
                withCredentials([file(credentialsId: 'npm-nexus-creds', variable: 'NPMRC')]) {
                    sh 'npm ci --userconfig $NPMRC'
                }
            }
        }

        stage('Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Analyse SonarQube + Quality Gate') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=Book_API \
                        -Dsonar.projectName=Book_API \
                        -Dsonar.sources=controllers,routes \
                        -Dsonar.tests=test \
                        -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                        -Dsonar.qualitygate.wait=true \
                        -Dsonar.qualitygate.timeout=120'''
                }
            }
        }

        stage('Publish (Nexus)') {
            steps {
                withCredentials([file(credentialsId: 'npm-nexus-creds', variable: 'NPMRC')]) {
                    sh 'npm version 1.0.$BUILD_NUMBER --no-git-tag-version'
                    sh 'npm publish --userconfig $NPMRC'
                }
            }
        }
    }

    post {
        success { echo 'Pipeline OK : paquet publié dans Nexus' }
        failure { echo 'Pipeline en échec : regarder le stage rouge' }
    }
}