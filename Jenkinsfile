pipeline {
    agent any

    environment {
        PATH = "/root/.local/bin:${env.PATH}"    // Ensure Ansible in PATH
        ANSIBLE_DIR = "ansible"
        INVENTORY = "staging"
#       GIT_CREDENTIAL_ID = "eaf80eb5-8136-4416-950c-ec3b7868f999" // SSH key for GitHub
        ANSIBLE_CONFIG = "${env.WORKSPACE}/ansible/ansible.cfg"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@github.com:kgrant224/ci_cd_project.git',
                    credentialsId: "${GIT_CREDENTIAL_ID}"
            }
        }

        stage('Install Ansible Roles') {
            steps {
                dir("${ANSIBLE_DIR}/playbooks") {
                    sh '''
                    # Install all roles defined in requirements.yml into ./roles
                    if [ -f requirements.yml ]; then
                        ansible-galaxy install -r requirements.yml -p ./roles
                    fi
                    '''
                }
            }
        }

        stage('Setup Servers') {
            steps {
                dir("${ANSIBLE_DIR}/playbooks") {
                    sh '''
                    # Run the setup_servers playbook using the correct inventory
                    ansible-playbook -i ../inventories/${INVENTORY} setup_servers.yml
                    '''
                }
            }
        }

        stage('Deploy App') {
            steps {
                dir("${ANSIBLE_DIR}/playbooks") {
                    sh '''
                    ansible-playbook -i ../inventories/${INVENTORY} deploy.yml
                    '''
                }
            }
        }

        stage('Post-Deployment Tests') {
            steps {
                echo "Running smoke tests..."
                sh 'curl -I http://localhost:8080'  // Update URL if needed
            }
        }
    }

    post {
        failure {
            echo 'Deployment failed — rolling back...'
            dir("${ANSIBLE_DIR}/playbooks") {
                sh '''
                ansible-playbook -i ../inventories/${INVENTORY} rollback.yml
                '''
            }
        }
    }
}
