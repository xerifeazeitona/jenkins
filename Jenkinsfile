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
                        rm -f ~/ip.txt
                        awk -F '"' '/192/{print \$2;exit;}' terraform.tfstate > ~/ip.txt
                    """
                    script {
                        server_ip = sh (script: 'cat ~/ip.txt', returnStdout: true).trim()
                        echo "The server IP is ${server_ip}"
                    }
                }
            }
        }  
        stage ('Configure - Ansible') {
            steps {
                dir('ansible') {
                    sh """
                        cd section6
                        rm -f hosts
                        echo "[web]\nweb1 ansible_host=${server_ip}" > hosts
                        while ! nc -z -w 5 ${server_ip} 22; do echo "waiting for server..."; sleep 5; done
                        ansible-playbook -i hosts playbook_web.yml
                    """
                }
            }
        }             
    }
}