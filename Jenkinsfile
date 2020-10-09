//Variables declaration
def server_ip
def username = "ubuntu"

pipeline {
    agent any
    triggers { pollSCM('H/15 * * * *') }
    environment {
        sitename = "www.candura.com"
        SERVERIP = "127.0.0.1"
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
                        terraform destroy -auto-approve
                    """
                    script {
                        //def command = 'terraform output | grep 192 | cut -d\'"\' -f2'
                        //def command = './get_ip.sh'
                        //echo command
                        server_ip = ${SERVERIP}
                        echo "The server IP is ${server_ip}"
                    }
                }
            }
        }       
    }
}