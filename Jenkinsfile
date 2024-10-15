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
                sh 'sudo apt-get update -y && sudo apt-get upgarde -y'
                sh 'sudo apt install -y wget tree unzip ansible python3-pip python2-apt'
            }
        }
        stage('Third Stage') {
            steps {
                echo "Third stage"
            }

        }
    }
}
