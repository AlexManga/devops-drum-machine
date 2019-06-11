Pipeline {
    agent any
    stages {
        stage('Checkout') {
            git credentialsId: 'Github Alexis', url: 'https://github.com/AlexManga/devops-drum-machine'
        }
        stage('Install dependencies') {
            ansiColor('xterm') {
                nodejs('node 12.4.0') {
                    sh 'npm install'
                }
            }
        }
        stage('Build') {
            ansiColor('xterm') {
                nodejs('node 12.4.0') {
                    sh 'npm run build'
                }
            }
        }
        stage('Test') {
            ansiColor('xterm') {
                nodejs('node 12.4.0') {
                    sh 'npm run test'
                }
            }
        }
        stage('Publish result') {
            junit 'tests-results/*.xml'
            archiveArtifacts 'public/'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'public', reportFiles: 'index.html', reportName: 'Appli', reportTitles: ''])
        }
    }
    chuckNorris()
}