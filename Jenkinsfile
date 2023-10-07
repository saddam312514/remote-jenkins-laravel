pipeline {
agent any
    stages {
        stage('Checkout') {
            steps {
               checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/saddam312514/laravel-jenkins.git']])
            }
        }
        stage('Remote Execution') {
            steps {
                script {
                    sshagent(credentials: ['ansible-test']) {
                        // Execute remote commands here
                        sh 'ssh ansadmin@192.168.1.20 "mkdir /home/ansadmin/anirak"'
                    }
                }
            }
        }
    }
}
