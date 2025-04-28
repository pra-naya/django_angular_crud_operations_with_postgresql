pipeline {
    agent any
    environment {
        FRONTEND_ZIP = "fullstack_test_frontend${BUILD_NUMBER}.zip"
        BACKEND_ZIP = "fullstack_test_backend${BUILD_NUMBER}.zip"
        SERVER = "${params.SERVER_USERNAME}@${params.SERVER_IP}"
        SERVERS_IPS = "${params.SERVER_IPS}"
        SERVER_USERNAME = "${params.SERVER_USERNAME}"
        SERVER_PASS = "${params.SERVER_PASS}"
    }
    stages {
        stage('Build') {
            steps {
                script {
                    if (params.BUILD_TARGET == 'BACKEND') {
                        echo 'No build required'
                    } else {
                        sh """
                            cd frontend; 
                            npm ci && ng build --configuration=production
                        """
                    }
                }
            }
        }
        stage('Zip Files') {
            steps {
                sh 'rm -rf .git'
                script {
                    if (params.BUILD_TARGET == 'BACKEND') {
                        sh """
                            zip -r ${BACKEND_ZIP} ./backend
                            rm -rf backend/*
                            mv ${BACKEND_ZIP} backend
                        """
                    } else if (params.BUILD_TARGET == 'FRONTEND') {
                        sh """
                            cd frontend
                            zip -r ../${FRONTEND_ZIP} dist/frontend/*
                            rm -rf ./*
                            mv ../${FRONTEND_ZIP} .
                        """
                    } else {
                        sh """
                            cd frontend
                            zip -r ../${FRONTEND_ZIP} dist/frontend/*
                            rm -rf ./*
                            mv ../${FRONTEND_ZIP} .

                            cd ..
                            zip -r ${BACKEND_ZIP} ./backend
                            rm -rf backend/*
                            mv ${BACKEND_ZIP} backend
                        """
                    }
                }
            }
        }
        stage('Transfer Files') {
            steps {
                script {
                    def server_list = params.SERVER_IPS.split("\n")
                    server_list.each() {
                        echo it
                    }

                    if (params.BUILD_TARGET == 'BACKEND') {
                        sh "scp backend/${BACKEND_ZIP} ${SERVER}:/tmp"
                    } else if (params.BUILD_TARGET == 'FRONTEND') {
                        sh "scp frontend/${FRONTEND_ZIP} ${SERVER}:/tmp"
                    } else {
                        sh "scp frontend/${FRONTEND_ZIP} ${SERVER}:/tmp"
                        sh "scp backend/${BACKEND_ZIP} ${SERVER}:/tmp"
                    } 
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    if (params.BUILD_TARGET == 'BACKEND') {
                        sh """
                            ssh ${SERVER} '
                                /home/${SERVER_USERNAME}/scripts/fullstack_test_backend_deploy.sh ${SERVER_USERNAME} ${BUILD_NUMBER} ${BACKEND_ZIP} ${SERVER_PASS}
                            '
                        """
                    } else if (params.BUILD_TARGET == 'FRONTEND') {
                        sh """
                            ssh ${SERVER} '
                                /home/${SERVER_USERNAME}/scripts/fullstack_test_frontend_deploy.sh ${SERVER_USERNAME} ${BUILD_NUMBER} ${FRONTEND_ZIP}
                            '
                        """
                    } else {
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
    }
}

