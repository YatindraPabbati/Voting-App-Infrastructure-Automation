
````markdown
# Voting App Infrastructure Automation

This repository provides automation for deploying the **Demo Voting App**. It combines the original Docker application with Ansible automation to simplify setup and management.

---

## Table of Contents
- [Project Overview](#project-overview)
- [Original Voting App](#original-voting-app)
- [Ansible Automation Role](#ansible-automation-role)
- [Directory Structure](#directory-structure)
- [Requirements](#requirements)
- [Variables](#variables)
- [Usage](#usage)
- [Docker Compose Stack](#docker-compose-stack)
- [Run in Kubernetes](#run-in-kubernetes)
- [Architecture](#architecture)
- [License](#license)

---

## Project Overview
This repository provides automation to deploy the Docker-based **Voting App** quickly on your servers. It ensures Docker is installed, configures the application, and starts the app stack using Docker Compose.  

The application itself is sourced from Docker's [example-voting-app](https://github.com/dockersamples/example-voting-app). All original source code belongs to Docker and is licensed under its original terms.  

## Ansible Automation Role

The **`example-voting-app-role`** automates deployment of the Voting App stack:

* Creates a deployment user
* Installs Git, Python3, and pip
* Clones the voting app repo
* Cleans up conflicting Docker packages
* Installs Docker and Docker Compose
* Deploys the Docker Compose stack
* Handles Docker restarts when needed

Supports both **Debian/Ubuntu** and **RedHat/CentOS** distributions.

---

## Directory Structure

```text
example-voting-app-role/
├── defaults/       # Default variables
├── files/          # Application source and build context (vote, result, worker)
├── handlers/       # Handlers (restart Docker)
├── meta/           # Role metadata
├── tasks/          # Main playbook tasks
├── templates/      # Docker Compose template
├── tests/          # Test inventory and playbooks
├── vars/           # Role variables
└── README.md       # This README
```
---

## Requirements

* Ansible >= 2.12
* Sudo privileges
* Internet access to fetch Docker packages and app repo

---

## Variables

Some key role variables (defined in `vars/main.yml`):

```yaml
voting_app_repo: "https://github.com/dockersamples/example-voting-app.git"
voting_app_demo_user: deployuser
voting_app_demo_user_home: "/home/{{ voting_app_demo_user }}"
voting_app_demo_user_dir: "{{ voting_app_demo_user_home }}/Voting-App-Infrastructure-Automation/ansible-automation"
docker_remove_conflicts: ...
docker_prerequisites: ...
docker_gpg_key_url: ...
docker_repo: ...
docker_packages: ...
```

---

## Usage

Run the playbook locally or on a remote server:

```bash
ansible-navigator run -i inventory -m stdout automate-example-voting-app.yml
```

This will:

1. Install Docker and dependencies
2. Clone the voting app repository
3. Deploy the Docker Compose stack automatically

---

## Docker Compose Stack

The stack uses the following services:

* **redis:** Redis cache
* **db:** Postgres database
* **vote:** Python web app
* **worker:** .NET worker
* **result:** Node.js web app

Docker Compose template is applied via the Ansible role to the deployment user’s directory.

---

## Architecture

![Architecture diagram](architecture.excalidraw.png)

* Front-end web app in Python (`vote`)
* Redis for messaging
* .NET worker consuming votes
* PostgreSQL database
* Node.js web app displaying results

Notes: Only one vote per client browser is accepted. This is a simple demo application to show how multiple languages, queues, and persistence work together in Docker.

---

## License

This repository contains two distinct parts, each under its own license:

1. **Ansible Automation Role (`example-voting-app-role`)**  
   - Author: Yatindra  
   - License: MIT License  
   - This role, including all tasks, handlers, templates, and variables, is fully authored by the repository owner and can be freely used, modified, and redistributed under the MIT terms.  

2. **Original Voting App (`vote`, `worker`, `result` directories and related files)**  
   - Source: [Docker Example Voting App](https://github.com/dockersamples/example-voting-app)  
   - License: Refer to Docker's original license in the source repository  
   - These files remain under their original license. No modifications to the source code license have been made.  

**Usage Note:**  
You are free to use the Ansible automation role as you like under MIT. When redistributing or modifying the Docker app itself, ensure you comply with Docker’s original license terms.  

