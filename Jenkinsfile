pipeline {
agent any
    stages {


            stage('Clean Workspace') {
            steps {
                // Delete the workspace before the build starts
                deleteDir()
           }
        }

        
        stage('Checkout') {
            steps {
               checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/saddam312514/laravel-jenkins.git']])
            }
        }

                stage('Build') {
            steps {
                // Build your PHP application (e.g., run composer, compile assets, etc.)
                //sh 'composer update && composer install'
                sh 'npm install'
                sh 'npm run production'
            }
        }

        stage('Build Tar File') {
            steps {
                // Create a tar file from your Laravel project files (replace with your tar command)
                sh 'tar -czf laravel-project.tar.gz *'
            }
        }
        stage('Archive Tar File') {
            steps {
                // Archive the tar file as an artifact
                archiveArtifacts artifacts: 'laravel-project.tar.gz', allowEmptyArchive: true
            }
        }

        stage('Deploy on your Staging env') {
            steps {
                script {
                    sshagent(credentials: ['ansible-test']) {
                    // Execute remote commands here
                    //sh 'ssh ansadmin@192.168.1.20 "sudo cp -rp /var/lib/jenkins/workspace/Deploy_Application_Staging_laravel/* /var/www/html/beta"'
                    sh 'ssh ansadmin@192.168.1.20 "sudo mkdir -p /var/www/html/beta"'
                    sh 'ssh ansadmin@192.168.1.20 "sudo chown -R ansadmin:ansadmin /var/www/html/beta"'
                    // Define the remote server details
                    def remoteServer = 'ansadmin@192.168.1.20'
                    def remoteFilePath = '/var/www/html/beta/'

                    // Define the local file path
                    def localFileEnv = '/var/lib/jenkins/workspace/.env'
                    def localFileProject = '/var/lib/jenkins/workspace/laravel-project-pipeline/'
                    sh "scp -r ${localFileEnv} ${remoteServer}:${remoteFilePath}"
                    sh "scp -r ${localFileProject}/*.tar.gz ${remoteServer}:${remoteFilePath}"
                 
                    sh 'ssh ansadmin@192.168.1.20 "cd /var/www/html/beta/ && sudo tar -xvf laravel-project.tar.gz"'
                    sh 'ssh ansadmin@192.168.1.20 "cd /var/www/html/beta/ && sudo composer update && cd /var/www/html/beta/ && sudo composer install && sudo php artisan key:generate && sudo php artisan migrate"'
                    sh 'ssh ansadmin@192.168.1.20 "sudo chown -R ansadmin:ansadmin /var/www/html/beta/"'
                    sh 'ssh ansadmin@192.168.1.20 "sudo chmod -R 777 /var/www/html/beta/"'
                    sh 'ssh ansadmin@192.168.1.20 "sudo rm -fr /var/www/html/beta/*.tar.gz"'
                    sh 'ssh ansadmin@192.168.1.20 "sudo setfacl -R --modify user:ansadmin:rwx /var/www/html/beta"'
                    }
                }
            }
        }
    }
}
