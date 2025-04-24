pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'cd frontend; npm install && ng build --configuration=production'
                sh 'pwd'
                sh 'find . -mindepth 1 -depth -not -path "./dist*" -exec rm -rf {} +'
                sh 'pwd'
            }
        }
        stage('Zip Files') {
            steps {
                sh """
                pwd
                cd ..  
                rm -rf .git
                zip -r fullstack_test_${BUILD_NUMBER}.zip .
                pwd
                ls
                echo ${BUILD_NUMBER}
                """
           }
        }
        stage('Transfer Files') {
            steps {
                sh "echo ${BUILD_NUMBER}"
                sh "scp fullstack_test_${BUILD_NUMBER}.zip ${params.SERVER_USERNAME}@${params.SERVER_IP}:/tmp"
           }
        }
        stage('Deploy') {
            steps {
                sh """
                    ssh ${params.SERVER_USERNAME}@${params.SERVER_IP} '
                        cd /home/${params.SERVER_USERNAME}/fullstack_test_deploy/ && 
                        zip -r ../fullstack_test_backup/backup_${BUILD_NUMBER}.zip . && 
                        rm -rf ./* && 
                        unzip /tmp/fullstack_test_${BUILD_NUMBER}.zip -d . && 
                        cd backend && 
                        python3 -m venv venv && 
                        source venv/bin/activate && 
                        pip install -r requirements.txt && 
                        echo "${SERVER_PASS}" | sudo -S systemctl restart fullstack_test'
                """
           }
        }
    }
}
