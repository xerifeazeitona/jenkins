//Variables declaration
def server_ip = "192.168.122.223"
def username = "ubuntu"

pipeline {
    agent any
    triggers { pollSCM('H/3 * * * *') }
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
        stage('Build and Test') {
            parallel {
                stage('Build') {
                    steps {
                        sh 'npm run build'
                    }
                }
                stage('Test') {
                    steps {
                        sh 'npm test -- --watchAll=false'
                    }
                }
            }
        }
        stage('Deploy') {
            when {branch 'main'}	
            steps {
                sh "scp -rv -o StrictHostKeyChecking=${params.STRICTHOST} -o UserKnownHostsFile=/dev/null build/* ${username}@${server_ip}:/var/www/${sitename}/"
            }
        }
    post {
        always {
            echo 'Send a notification message via email or something else'
        }
    }
    }
}