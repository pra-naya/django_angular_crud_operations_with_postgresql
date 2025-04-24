pipeline {
    agent any
    stages {
        stage('Zip Files') {
            steps {
                sh """
                rm -rf .git
                zip -r fullstack_test_${BUILD_NUMBER}.zip ./backend
                """
           }
        }
        stage('Transfer Files') {
            steps {
                sh "scp fullstack_test_${BUILD_NUMBER}.zip ${params.SERVER_USERNAME}@${params.SERVER_IP}:/tmp"
           }
        }
        stage('Deploy') {
            steps {
                sh """
                    ssh ${params.SERVER_USERNAME}@${params.SERVER_IP} "cd /home/${params.SERVER_USERNAME}/fullstack_test_deploy/ && zip -r ../fullstack_test_backup/backup_${BUILD_NUMBER}.zip . && rm -rf ./* && unzip /tmp/fullstack_test_${BUILD_NUMBER}.zip -d . && cd backend && python3 -m venv venv && source venv/bin/activate && pip install -r requirements.txt && cp /home/${params.SERVER_USERNAME}/fullstack_test_env/.env . && echo "${SERVER_PASS}" | sudo -S systemctl restart fullstack_test"
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
