pipeline {
    agent any
    tools {
        nodejs 'node 12.4.0'
    }
    stages {
        stage('Install dependencies') {
            steps {
                ansiColor('xterm') {
                    sh 'npm install'
                }
            }
        }
        stage('Build') {
            steps {
                ansiColor('xterm') {
                    sh 'npm run build'
                }
            }
        }
        stage('Test') {
            steps {
                ansiColor('xterm') {
                    sh 'npm run test'
                }
            }
        }
        stage('Publish result') {
            steps {
                junit 'tests-results/*.xml'
                archiveArtifacts 'public/'
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'public', reportFiles: 'index.html', reportName: 'Appli', reportTitles: ''])
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker run -d --name alexis -p 8081:80 -p 2222:22 iliyan/docker-nginx-sshd'
            }
        }
    }
}