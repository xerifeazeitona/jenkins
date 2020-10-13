//Variables declaration
def server_ip
def username = "ubuntu"

pipeline {
    agent { label 'agentorange' }
    triggers { pollSCM('H/15 * * * *') }
    environment {
        sitename = "www.candura.com"
    }
    parameters {
        choice(name: 'Provision', choices: ['No', 'Yes', 'Destroy'], description: 'Toggle provisioning an instance')
        choice(name: 'Configure', choices: ['No', 'Yes'], description: 'Toggle configuring an instance')
    }
    stages {
        stage ('Provision - Terraform') {
            agent { label 'master' }
            when {expression { params.Provision == 'Yes' }}
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
        stage ('Destroy Provision') {
            agent { label 'master' }
            when {expression { params.Provision == 'Destroy' }}
            steps {
                dir('ansible') {
                    sh """
                        cd terraform_web_server
                        terraform destroy -auto-approve
                        rm -f ~/ip.txt
                    """
                }
            }
        }  
        stage ('Configure - Ansible') {
            agent { label 'master' }
            when {expression { params.Configure == 'Yes' }}
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
                sshagent(credentials : ['agentkey']) {
                    sh "scp -rv -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null build/* ${username}@${server_ip}:/var/www/${sitename}/"
                }
            }
        }
    }
}