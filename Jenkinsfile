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
                shWithXterm('npm run build')
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
            }
        }
        stage('Remove site on server') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-nginx-ssh', passwordVariable: 'password', usernameVariable: 'username')]) {
                        def remote = [:]
                        remote.name = 'docker nginx ssh'
                        remote.host = 'localhost:2222'
                        remote.user = userName
                        remote.password = password
                        remote.allowAnyHosts = true
                        sshRemove remote: remote, path: "/sites"
                    }
                }
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
                        transfers: [sshTransfer(cleanRemote: true, excludes: '', execCommand: 'ls /sites', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false,
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
    post {
        cleanWs()
    }
}

def shWithXterm(String command) {
    ansiColor('xterm') {
        sh "${command}"
    }
}