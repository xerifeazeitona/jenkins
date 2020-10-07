pipeline {
    agent any
    stages {
        stage('Clone Git') {
            steps {
                checkout scm
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Deploy') {
            when {branch 'main'}	
            steps {
                sh 'scp -rv -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null /var/lib/jenkins/workspace/Section4_main/build/* ubuntu@192.168.122.223:/var/www/www.candura.com/'
            }
        }
    }
}