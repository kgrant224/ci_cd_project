pipeline {
    agent any

    environment {
        ANSIBLE_DIR = "ansible"
        INVENTORY = "staging"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-org/ci_cd_project.git'
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
                sh 'curl -I http://your-server-address'
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
