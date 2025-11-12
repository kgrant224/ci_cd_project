pipeline {
    agent any

    environment {
        PATH = "/root/.local/bin:${env.PATH}"
        ANSIBLE_DIR = "ansible"
        INVENTORY = "staging"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@github.com:kgrant224/ci_cd_project.git',
                    credentialsId: 'git'
            }
        }

        stage('Setup Servers') {
            steps {
                sh """
                ansible-playbook -i ${ANSIBLE_DIR}/inventories/${INVENTORY} \
                ${ANSIBLE_DIR}/playbooks/setup_servers.yml
                """
            }
        }

        stage('Deploy App') {
            steps {
                sh """
                ansible-playbook -i ${ANSIBLE_DIR}/inventories/${INVENTORY} \
                ${ANSIBLE_DIR}/playbooks/deploy.yml
                """
            }
        }

        stage('Post-Deployment Tests') {
            steps {
                echo "Running smoke tests..."
                sh 'curl -I http://localhost:8080'   // update this as needed
            }
        }
    }

    post {
        failure {
            echo 'Deployment failed — rolling back...'
            sh """
            ansible-playbook -i ${ANSIBLE_DIR}/inventories/${INVENTORY} \
            ${ANSIBLE_DIR}/playbooks/rollback.yml
            """
        }
    }
}
