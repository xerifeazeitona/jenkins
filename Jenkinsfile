//Variables declaration
def server_ip
def username = "ubuntu"

pipeline {
    agent any
    triggers { pollSCM('H/15 * * * *') }
    environment {
        sitename = "www.candura.com"
    }
    parameters {
        choice(name: 'STRICTHOST', choices: ['No', 'Yes'], description: 'Strict host key checking')
    }
    stages {
        stage ('Provision - Terraform') {
            steps {
                dir('ansible') {
                    git branch: 'master',
                    credentialsId: 'eec3318a-0c54-438e-96b1-6310ffeaaee0',
                    url: 'https://github.com/xerifeazeitona/ansible.git'
                    sh """
                        cd terraform_web_server
                        terraform init
                        terraform apply -auto-approve
                        awk -F '"' '/192/{print \$2;exit;}' terraform.tfstate > ~/ip.txt
                        cat ~/ip.txt
                    """
                    script {
                     //   server_ip = sh (script: 'awk -F \'"\' \'/192/{print $2;exit;}\' terraform.tfstate', returnStdout: true)
                        echo "The server IP is ${server_ip}"
                    }
                }
            }
        }       
    }
}