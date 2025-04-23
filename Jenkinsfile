pipeline {
    agent any
    stages {
        stage('Check') {
            steps {
                sh 'ls -la'
                sh 'echo ${params.SERVER_IP}' 
           }
        }   
    }
}
