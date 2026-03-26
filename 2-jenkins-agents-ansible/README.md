# Exercise 2 - Jenkins Agents with Ansible

**Goal**: Set up 5 Jenkins agents locally using Docker Compose and Ansible

## Prerequisites

1. Jenkins controller running (from Exercise 1)
2. Ansible installed: `pip install ansible`

## Task 1: Create all the containers

1. Copy the Docker Compose file from Exercise 1 to this folder
2. Add a new container (services) to the Docker compose. These is one of the worker nodes

    ```yaml
    services:
    ...
      agent-01:
        image: ubuntu:22.04
        container_name: agent-01
        hostname: agent-01
        command: sleep infinity
    ...
    ```

3. Add 4 more agents (for a total of five: `agent-01` to `agent-05`)
4. Start Docker Compose (it should start 6 containers in total): `docker compose -p jenkins up`

## Task 2: Create nodes in the Jenkins Controller

1. Log in to Jenkins at http://localhost:8080
2. Press the configure (Gear) button > Nodes
3. Press **New Node**
4. Create permanent agent `agent-01`
  - Set **Remote Work Directory** to `/home/jenkins/agent`
5. Press **Save**
6. Open `agent-01`
7. Near "Run from agent command line: (Unix)" copy the secret for that node (e.g., `6b4e75ff976856f8874e5e9bf0b57bb619d1ea2a531e1c861b9a9e93ebdea039`)
8. Repeat the procedure 4 more times to create `agent-02` to `agent-05`. Save each individual secret

## Task 3: Prepare the inventory and secrets

1. Examine the inventory files `inventory.ini`
2. Create a config file in `host_vars` called `host_vars/agent-01.yml`
3. Add the following line:

    ```yaml
    jenkins_agent_secret: YOUR_JENKINS_AGENT_01_SECRET
    ```

4. Test if Ansible is picking up the values with:

    ```shell
    ansible-inventory -i inventory.ini --host agent-01

    {
        "ansible_connection": "docker",
        "ansible_host": "agent-01",
        "ansible_python_interpreter": "/usr/bin/python3",
        "ansible_user": "root",
        "jenkins_agent_home": "/home/jenkins/agent",
        "jenkins_agent_secret": "YOUR_JENKINS_AGENT_01_SECRET",
        "jenkins_url": "http://jenkins-controller:8080"
    }
    ```
5. Repeat the steps for `agent-02` to `agent-05`

## Task 4: Install worker nodes

1. Examine the Ansible playbook: `playbook-jenkins-node.yml`
2. Run the playbook. This configures the containers to run Jenkins nodes

    ```sh
    ansible-playbook -i inventory.ini playbook-jenkins-node.yml
    ```

3. Check that it passed. If you need to restart you can stop and start Docker Compose. It should reset the agent containers.

## Task 5: Check that nodes have connected to Jenkins controllers

1. Log in to Jenkins at http://localhost:8080
2. All agents should be in connected status (none "offline")


## How It Works

- **Docker Compose**: Creates 5 Ubuntu containers
- **Ansible Playbook**: 
  - Downloads `agent.jar` from Jenkins controller
  - Creates launcher script with JNLP connection details
  - Enables and starts the service

## File Structure

```
2-jenkins-agents-ansible/
├── docker-compose.yml      # 5 agent containers
├── inventory.ini           # Ansible inventory
├── requirements.yml        # Ansible collections
├── group_vars/             # Ansible group config
│   ├── jenkins_agents.yml
├── host_vars/              # Ansible host config
│   ├── agent-01.yml
│   ├── agent-02.yml
│   ├── agent-03.yml
│   ├── agent-04.yml
│   ├── agent-05.yml
└── README.md
```

