## Making Playbooks

### Step 1: SSH into the Ansible Controller (Using a new Gitbash)

- 1) Sudo Update && Upgrade
- 2) SSH into the controller
- 3) Go into the .ssh folder to test if we can connect to the app
- 4) SSH into the app using the controller
- 5) type `exit` once you've tested

### Step 2: Create a web host

- 1) `cd etc/ansible/` to see hosts then `cd hosts`
- 2) `sudo nano hosts` and add:
    ```
  [web] 
  
  ec2-instance ansible_host=<app ip> ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubuntu/.ssh/tech254.pem
  ```

- 3) `sudo ansible web -m ping` should return this:

```
  ec2-instance | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"}
```

### Step 3: Making a playbook using Yaml

- 1) `sudo nano install-nginx.yml`

```
  # create a playbook to provision nginx web server in web-node
---
# three dashes starts a yaml file
# where do you want to install or run this playbook
- hosts: web

# find the facts
  gather_facts: yes
# provide admin access to this playbook - use sudo
  become: true
# provde the actual instructions - install nginx
  tasks:
  - name: provision/install/configure Nginx
    apt: pkg=nginx state=present
# ensure nginx is running/enable
```

- 2) `sudo ansible-playbook install-nginx.yml` to run the yaml file
- 3) Check if it's done it by using the public IP of the app, Nginx should be running

### Step 4 (Installing NodeJS using Yaml Playbook)

- 1) `sudo nano install-nodejs.yml`
```
---
# where do you want to install or run this playbook
- hosts: web

# find the facts
  gather_facts: yes

# provide admin access to this playbook - use sudo
  become: true

# provide the actual instructions - install NodeJS
  tasks:
  - name: Install the NodeSource Node.js 12.x release PPA
    shell: "curl -sL https://deb.nodesource.com/setup_12.x | bash -"

  - name: Install Node.js
    apt: pkg=nodejs state=present

  - name: Check Node.js version
    shell: "node -v"
    register: node_version

  - name: Display Node.js version
    debug:
      msg: "Node.js version is {{ node_version.stdout }}"

  - name: Check NPM version
    shell: "npm -v"
    register: npm_version

  - name: Display NPM version
    debug:
      msg: "NPM version is {{ npm_version.stdout }}"
```









