# DevOps Personal Project 1
Mini DevOps project for educational purposes, made using: Ansible, Jenkins, Docker-Compose, Nginx, MySQL and PHPMyAdmin (Apache2 Server).

## Overview
The project demonstrates using Jenkins server to use a pipeline Jenkinsfile to deploy a docker-compose mini-cluster (MySQL DB with PHPMyAdmin as Apache Server Interface and Nginx to add HTTPs layer) on a remote deployment machine. This project was related to a task given to me @SiliconMind DevOps internship.
In my use-case the remote Jenkins server didn't have Ansible installed and only used one deployment machine. So I couldn't Invoke the ansible playbook from Jenkins, I invoked it from my local machine to the deployment_servers group.
But, in case of a fully owned Jenkins server, you can install Ansible and use the pipeline to run the ansible playbook and have it transfer the docker-compose files as well to all/specific target group.

So, in this example I'll use Ansible to provision and Jenkins to run docker-compose up on one deployment machine.

## Instructions

- Machines are assumed to be Debian.
  
- Difference between `docker-compose.yml` and `docker-compose2.yml` is that the first pulls every app as an image and configures everything for just one remote host, the latter pulls only MySQL and PMA while nginx is installed natively on the remote host and meant to be used with Ansible.

- You need to configure your Jenkins/Ansible SSH access to your remote machines first ideally by SSH keys.

- Generate an SSH key and transfer it manually `ssh-keygen` and `ssh-copy-id -i id_rsa.pub <destination_ip_or_alias>`
or distribute it using Ansible (if multiple deployment servers are used) providing a target group (Inventory/hosts) and giving their passwords (given that password-authentication is enabled) for speed.

- Machines ssh password and become password are stored in the encrypted file `group_vars/deployment_servers/vault` using `ansible-vault encrypt vault` and you need to set your own for security. You can use `ansible-vault edit vault` and use the secret key to decrypt it when prompted.

- Invoke Ansible using `ansible-playbook -i inventory playbook.yml --ask-vault-pass` on your ansible machine and give out the vault password manually or add it to the jenkins pipeline `Jenkinsfile` and give out the vault password as a file using `ansible-playbook -i inventory --vault-password-file ../vault playbook.yml`.



## Work Diagram
![ansible-compose-jenkins-p1](https://github.com/Asserzayed/jenkins-ansible-compose-p1/assets/17936594/a89d84e6-59fb-4c87-bc42-31c79e81ca22)

## Workflow
1 - Ansible Provisions and Configures (Installs Docker, Docker-Compose, Nginx and transfers Nginx `.conf.j2` jinja file and configures user group, firewall and SELinux).

2 - Jenkins pull the repo and transfers the `docker-compose.yml` `docker-compose2.yml` `.env` `.dockerignore` to the target machine.

3 - Jenkins runs `docker-compose up -d`

## Variables
Variables found in `ansible_playbook/group_vars/deployment_servers/vars`
- `FQDN` the local domain name you want to access the PHPMyAdmin page. You need to set that in your `/etc/hosts` to map to the deployment server of choice. traffic coming from anywhere other than `FQDN` will give out `ERROR 403`
- `user_name` user name on the deployment machine used in `add_docker_user` role and used by `ansible_user`.
- `server_name` the ip of the server used in jinja file best sat as `_`.
- `compose_version` used to specify the docker-compose version to download from Github.

## Ansible roles file strcuture
```
├── group_vars
│   └── deployment_servers
│       ├── vars
│       └── vault
├── inventory
├── playbook.yml
├── roles
│   ├── add_docker_user
│   │   └── tasks
│   │       └── main.yml
│   ├── adjust_selinux
│   │   └── tasks
│   │       └── main.yml
│   ├── config_firewall
│   │   └── tasks
│   │       └── main.yml
│   ├── config_nginx
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── templates
│   │       └── nginx.conf.j2
│   ├── install_docker_compose
│   │   └── tasks
│   │       └── main.yml
│   ├── install_dockerce
│   │   └── tasks
│   │       └── main.yml
│   ├── install_nginx
│   │   └── tasks
│   │       └── main.yml
│   └── ready_deploy
│       └── tasks
│           └── main.yml
└── vault
```
