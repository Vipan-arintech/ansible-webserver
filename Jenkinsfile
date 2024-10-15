pipeline {
    agent { label "agentfarm" }
    stages {
        stage('Delete the workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Installing Ansible') {
            steps {
                script {
                    def ansible_exists = fileExists('/usr/bin/ansible')
                    if (ansible_exists) {
                        echo "Skipping ansible install - already exists"
                    } else {
                        sh 'sudo apt-get update -y && sudo apt-get upgrade -y'
                        sh 'sudo apt install -y wget tree unzip ansible python3-pip python3-apt'
                    }
                }
            }
        }
        stage('Download ansible code') {
            steps {
                git credentialsId: 'git-repo-cred', url: 'git@github.com:Vipan-arintech/ansible-webserver.git'
            }
        }
        stage('Run ansible-lint against playbooks') {
            steps {
               sh 'docker run --rm -v $WORKSPACE/playbooks:/data cytopia/ansible-lint:4 apache-install.yml'
               sh 'docker run --rm -v $WORKSPACE/playbooks:/data cytopia/ansible-lint:4 website-update.yml'
               sh 'docker run --rm -v $WORKSPACE/playbooks:/data cytopia/ansible-lint:4 website-test.yml' 
            }
        }
        // Add further stages here
        stage('Install apache & update website') {
            steps {
                sh 'export ANSIBLE_HOST_KEY_CHECKING=False && ansible-playbook -u ubuntu --private-key /home/ubuntu/ubuntu.pem -i $WORKSPACE/host_inventory $WORKSPACE/playbooks/apache-install.yml'
                sh 'export ANSIBLE_ROLES_PATH=/opt/jenkins/workspace/ansible-pipeline/roles && ansible-playbook -u ubuntu --private-key /home/ubuntu/ubuntu.pem -i $WORKSPACE/host_inventory $WORKSPACE/playbooks/website-update.yml'
            }
        }
        stage('Test Website') {
            steps {
                sh 'export ANSIBLE_ROLES_PATH=/opt/jenkins/workspace/ansible-pipeline/roles && ansible-playbook -u ubuntu --private-key /home/ubuntu/ubuntu.pem -i $WORKSPACE/host_inventory $WORKSPACE/playbooks/website-test.yml'
            }
        }
    }
    post {
        success {
            slackSend color: 'good', message: "Build ${env.JOB_NAME} ${env.BUILD_NUMBER} was successful! :)"
        }
        failure {
            slackSend color: 'danger', message: "Build ${env.JOB_NAME} ${env.BUILD_NUMBER} failed :("
        }
    }
}
