//Variables declaration
def server_ip = "192.168.122.223"
def username = "ubuntu"

pipeline {
    agent any
    environment {
        sitename = "www.candura.com"
    }
    parameters {
        choice(name: 'STRICTHOST', choices: ['No', 'Yes'], description: 'Strict host key checking')
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
                sh "scp -rv -o StrictHostKeyChecking=${params.STRICTHOST} -o UserKnownHostsFile=/dev/null build/* ${username}@${server_ip}:/var/www/${sitename}/"
            }
        }
    }
}