pipeline {
    agent any
    stages {
        stage('Zip Files') {
            steps {
                sh 'rm -rf .git'
                sh "zip -r fullstack_test_$BUILD_NUMBER.zip ./backend"
           }
        }
        stage('Transfer Files') {
            steps {
                sh "scp -P 3022 fullstack_test_$BUILD_NUMBER.zip ubuntu@${params.SERVER_IP}:/home/ubuntu/fullstack_test_deploy/backend"
           }
        }
        stage('Deploy') {
            steps {
                sh """
                    ssh -p 3022 ubuntu@${params.SERVER_IP} "cd /home/ubuntu/fullstack_test_deploy/backend && zip -r ../../fullstack_test_backup/backup_$BUILD_NUMBER.zip && rm -rf ./* && unzip /tmp/fullstack_test_$BUILD_NUMBER.zip -d . && sudo systemctl restart fullstack_test"
                """
                sh "echo ${params.SERVER_IP}"
           }
        }
        stage('Check') {
            steps {
                sh 'ls -la'
                sh "echo ${params.SERVER_IP}"
           }
        }   
    }
}
