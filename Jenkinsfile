pipeline {
    agent any
    environment {
        FRONTEND_ZIP = "fullstack_test_frontend${BUILD_NUMBER}.zip"
        BACKEND_ZIP = "fullstack_test_backend${BUILD_NUMBER}.zip"
        SERVER = "${params.SERVER_USERNAME}@${params.SERVER_IP}"
        SERVER_USERNAME = "${params.SERVER_USERNAME}"
        SERVER_PASS = "${params.SERVER_PASS}"
    }
    stages {
        stage('Build') {
            steps {
                sh """
                    cd frontend; 
                    npm ci && ng build --configuration=production
                """
            }
        }
        stage('Zip Files') {
            steps {
                sh """
                    cd frontend
                    zip -r ../${FRONTEND_ZIP} dist/frontend/*
                    rm -rf ./*
                    mv ../${FRONTEND_ZIP} .

                    cd ..
                    rm -rf .git
                    zip -r ${BACKEND_ZIP} ./backend
                    rm -rf backend/*
                    mv ${BACKEND_ZIP} backend
                """
           }
        }
        stage('Transfer Files') {
            steps {
                sh "scp frontend/${FRONTEND_ZIP} ${SERVER}:/tmp"
                sh "scp backend/${BACKEND_ZIP} ${SERVER}:/tmp"
           }
        }
        stage('Deploy') {
            steps {
                sh """
                    ssh ${SERVER} '
                        /home/${SERVER_USERNAME}/scripts/fullstack_test_frontend_deploy.sh ${SERVER_USERNAME} ${BUILD_NUMBER} ${FRONTEND_ZIP}
                        
                        /home/${SERVER_USERNAME}/scripts/fullstack_test_backend_deploy.sh ${SERVER_USERNAME} ${BUILD_NUMBER} ${BACKEND_ZIP} ${SERVER_PASS}
                    '
                """
           }
        }
    }
}

