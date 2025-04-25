pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh """
                    cd frontend; 
                    npm install && ng build --configuration=production
                """
            }
        }
        stage('Zip Files') {
            steps {
                sh """
                    ls
                    pwd
                    cd frontend
                    zip -r ../fullstack_test_frontend_${BUILD_NUMBER}.zip dist
                    rm -rf ./*
                    mv ../fullstack_test_frontend_${BUILD_NUMBER}.zip .

                    cd ..
                    ls 
                    pwd
                    rm -rf .git
                    zip -r fullstack_test_backend${BUILD_NUMBER}.zip ./backend
                    rm -rf backend/*
                    mv fullstack_test_backend${BUILD_NUMBER}.zip backend
                    pwd
                    ls
                    echo ${BUILD_NUMBER}
                """
           }
        }
        stage('Transfer Files') {
            steps {
                sh "echo ${BUILD_NUMBER}"
                sh 'pwd'
                sh 'ls'
                sh 'ls frontend'
                sh 'ls backend'
                sh "scp frontend/fullstack_test_frontend${BUILD_NUMBER}.zip ${params.SERVER_USERNAME}@${params.SERVER_IP}:/tmp"
                sh "scp backend/fullstack_test_backend${BUILD_NUMBER}.zip ${params.SERVER_USERNAME}@${params.SERVER_IP}:/tmp"
           }
        }
        stage('Deploy') {
            steps {
                sh """
                    ssh ${params.SERVER_USERNAME}@${params.SERVER_IP} '
                        cd /home/${params.SERVER_USERNAME}/fullstack_test_deploy/ && 
                        zip -r ../fullstack_test_backup/backup_${BUILD_NUMBER}.zip . && 
                        rm -rf ./* && 
                        unzip /tmp/fullstack_test_frontend${BUILD_NUMBER}.zip -d ./frontend && 
                        unzip /tmp/fullstack_test_backend${BUILD_NUMBER}.zip -d ./backend && 
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

