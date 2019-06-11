pipeline {
    agent any
    tools {
        nodejs 'node 12.4.0'
    }
    stages {
        stage('Init & Install dependencies') {
            steps {
                chuckNorris()
                shWithXterm('npm install')
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
                shWithXterm('npm run test')
            }
        }
        stage('Publish result') {
            steps {
                junit 'tests-results/*.xml'
                archiveArtifacts 'public/'
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'public', reportFiles: 'index.html', reportName: 'Appli', reportTitles: ''])
            }
        }
//        stage('Deploy with docker cp') {
//            steps {
//                dir('public') {
//                    shWithXterm('sudo docker cp . alexis:/sites/')
//                }
//            }
//        }
        stage('Deploy with plugins') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'localhost docker nginx',
                        transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ls /sites', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false,
                                patternSeparator: '[, ]+', remoteDirectory: '/sites', remoteDirectorySDF: false, removePrefix: 'public', sourceFiles: 'public/')], usePromotionTimestamp: false,
                        useWorkspaceInPromotion: false, verbose: true)])
            }
        }
        stage('Integration test') {
            steps {
                shWithXterm('curl localhost:8008')
            }
        }
    }
}

def shWithXterm(String command) {
    ansiColor('xterm') {
        sh "${command}"
    }
}