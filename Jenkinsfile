pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }



    stages {
        stage('Install (via Nexus)') {
            steps {
                withCredentials([file(credentialsId: 'npm-nexus-creds', variable: 'NPMRC')]) {
                   sh 'npm ci --userconfig "$NPMRC"'
                }
            }
        }

        stage('Tests') {
            steps {
                sh 'npm test'
            }
        }

        // stage('Analyse SonarQube + Quality Gate') {
        //     steps {
        //         withSonarQubeEnv('SonarQube') {
        //             sh ''
        //         }
        //     }
        // }

        stage('Publish (Nexus)') {
            steps {
                withCredentials([file(credentialsId: 'npm-nexus-creds', variable: 'NPMRC')]) {
                    sh 'npm version 1.0.$BUILD_NUMBER --no-git-tag-version'
                 sh 'npm publish --userconfig "$NPMRC"'
                }
            }
        }
    }

    post {
        success { echo 'Pipeline OK : paquet publié dans Nexus' }
        failure { echo 'Pipeline en échec : regarder le stage rouge' }
    }
}
