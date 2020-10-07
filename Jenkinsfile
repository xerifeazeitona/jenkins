//Variables declaration
def server_ip = "192.168.122.223"
def username = "ubuntu"

pipeline {
    agent any
    environment {
        sitename = "www.candura.com"
    }
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
                sh "scp -rv -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null build/* ${username}@${server_ip}:/var/www/${sitename}/"
            }
        }
    }
}