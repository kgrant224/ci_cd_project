# CI/CD Project with Jenkins + Ansible

## Overview
This project demonstrates a full CI/CD pipeline using Jenkins and Ansible for automated provisioning, deployment, and rollback of a static web application served by Nginx.

## Project Structure
ci_cd_project/
├── Jenkinsfile
├── ansible/
│ ├── inventories/
│ │ ├── staging
│ │ └── production
│ ├── playbooks/
│ │ ├── setup_servers.yml # installs dependencies, prepares hosts
│ │ ├── deploy.yml # deploys new app version (static files via Nginx)
│ │ └── rollback.yml # restores previous app version from backup
│ └── roles/
│ ├── webserver/
│ │ ├── tasks/main.yml
│ │ └── templates/nginx.conf.j2
│ └── app/
│ ├── tasks/main.yml
│ └── files/app_package.tar.gz
└── README.md


## Features
- Automated provisioning of staging/production hosts.
- Deployment of static web app via Nginx.
- Backup and rollback mechanism for previous app versions.
- Fully integrated with Jenkins for CI/CD.

## Usage

1. **Configure inventory files**  
   Update `ansible/inventories/staging` and `ansible/inventories/production` with your hosts.

2. **Ensure backup directories exist**  
   On app servers:
   ```bash
   mkdir -p /opt/app/backup


Push the repository to GitHub
Make sure your Jenkins server can access the repo.

Run Ansible playbooks manually (optional):

# Setup servers
ansible-playbook -i ansible/inventories/staging ansible/playbooks/setup_servers.yml

# Deploy app
ansible-playbook -i ansible/inventories/staging ansible/playbooks/deploy.yml

# Rollback app
ansible-playbook -i ansible/inventories/staging ansible/playbooks/rollback.yml