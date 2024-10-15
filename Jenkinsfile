pipeline {
    agent {label "agentfarm"}
    stages {
        stage('Delete the workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Installing Ansible') {
            steps {
                scripts { 
                def ansible_exists = fileExists '/usr/bin/ansible'
                if (ansible_exists == true) {
                    echo "Skipping ansible install - already exists"
                } else {
                sh 'sudo apt-get update -y && sudo apt-get upgarde -y'
                sh 'sudo apt install -y wget tree unzip ansible python3-pip python2-apt'
                }
                } 
        }
        stage('Third Stage') {
            steps {
                echo "Third stage"
            }
        }
    }
}
}
