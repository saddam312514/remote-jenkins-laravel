
pipeline {
    agent any
    stages {

        stage('Clean Workspace') {
            steps {
                // Delete the workspace before the build starts
                deleteDir()
           }
        }
          stage('Cloning Git') {
            steps {
              // checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/saddam312514/laravel-jenkins.git']])
                checkout scm
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
   // stage('Archive as ZIP') {
   //          steps {
   //              // Archive the Laravel project as a ZIP file using the full path
   //              archiveArtifacts allowEmptyArchive: true, artifacts: '**/*'
   //          }
   //      }

        //      stage('Archive as ZIP') {
        //     steps {
        //         // Archive the Laravel project as a ZIP file
        //         archiveArtifacts allowEmptyArchive: true, artifacts: '**/*', excludes: ''
        //         // script {
        //         //     // Rename the archived ZIP file to a specific name (optional)
        //         //     def zipFileName = "/var/lib/jenkins/workspace/Package_install_Laravel.zip"
        //         //     sh "mv *zip ${zipFileName}"
        //         // }
        //     }
        // }


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

         stage('Deploy in Staging Environment'){
            steps{
                    timeout(time:5, unit:'DAYS'){
                     input message:'Approve Staging Deployment?'
                 }

         
                 build job: 'Deploy_Application_Staging_laravel'
                 // Define the source and destination directories 
                // // Create the destination directory if it doesn't exist
                   sh "sudo mkdir -p /var/www/html/laravel"

               
                    sh "sudo cp -rp /var/lib/jenkins/workspace/Deploy_Application_Staging_laravel/* /var/www/html/laravel"
                    sh 'sudo cp -r /var/lib/jenkins/workspace/.env /var/www/html/laravel'
                 
                    sh 'cd /var/www/html/laravel/ && sudo tar -xvf laravel-project.tar.gz'
                    sh 'cd /var/www/html/laravel/ && sudo composer update && cd /var/www/html/laravel/ && sudo composer install && sudo php artisan key:generate && sudo php artisan migrate'
                    sh 'sudo chown -R www-data:www-data /var/www/html/laravel/'
                    sh 'sudo chmod -R 775 /var/www/html/laravel/storage'
                    sh 'sudo rm -fr /var/www/html/laravel/*.tar.gz'
            
             }
            
         }
         stage('Deploy to Production'){
             steps{
                 timeout(time:5, unit:'DAYS'){
                     input message:'Approve PRODUCTION Deployment?'
                 }
                 build job: 'Deploy_Application_Prod_laravel'


                 sh "sudo mkdir -p /var/www/html/live"

                    sh "sudo cp -rp /var/lib/jenkins/workspace/Deploy_Application_Prod_laravel/* /var/www/html/live"
                    sh 'sudo cp -r /var/lib/jenkins/workspace/.env /var/www/html/live'
                 
                    sh 'cd /var/www/html/live/ && sudo tar -xvf laravel-project.tar.gz'
                    sh 'cd /var/www/html/live/ && sudo composer update && cd /var/www/html/live/ && sudo composer install && sudo php artisan key:generate && sudo php artisan migrate'
                    sh 'sudo chown -R www-data:www-data /var/www/html/live/'
                    sh 'sudo chmod -R 775 /var/www/html/live/storage'
                    sh 'sudo rm -fr /var/www/html/live/*.tar.gz'
             }
         }
    }
}
