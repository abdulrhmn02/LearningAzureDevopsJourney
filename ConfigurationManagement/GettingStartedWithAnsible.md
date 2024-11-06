

---

# Ansible Setup and Nginx Installation Playbook

This project demonstrates the process of setting up Ansible to manage multiple virtual machines (VMs), generate SSH keys for secure communication, and then create and run an Ansible playbook to install and start Nginx on the target servers.

## Prerequisites

Before you begin, ensure that you have the following:

- **VMs**: Two virtual machines (VMs) for the Ansible control node and the target node.
- **Ansible**: Ansible installed on the control node.
- **SSH Access**: Ensure SSH access is set up between the control node and target VMs.
  
## Setup Steps

### 1. Create and Prepare Virtual Machines

1. **Create two VMs**: One for the Ansible control node and another as the target (managed) node.
2. **Update the VMs**: Ensure that both VMs are up-to-date.
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

### 2. Install Ansible on Control Node

1. Install Ansible on the control node (VM1):
   ```bash
   sudo apt update
   sudo apt install -y ansible
   ```

2. Verify the Ansible installation:
   ```bash
   ansible --version
   ```

### 3. Generate SSH Key Pair on Control Node

1. On the control node (VM1), generate an SSH key pair:
   ```bash
   ssh-keygen
   ```

   This will create a public and private key pair, typically stored in `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`.

2. Copy the public key (`~/.ssh/id_rsa.pub`) to the target node (VM2) to allow SSH access without a password:
   ```bash
   ssh-copy-id user@<target-vm-ip>
   ```

3. Verify that you can SSH into the target node (VM2) without a password:
   ```bash
   ssh user@<target-vm-ip>
   ```

### 4. Set Up Ansible Inventory File

Create an Ansible inventory file to define your target hosts (the servers that Ansible will manage). 

1. Create an `inventory` file:
   ```bash
   vim inventory
   ```

2. Add your target server IPs, grouped into categories (optional):
   ```ini
   [group1]
   172.31.45.2
   172.31.45.4
   172.31.45.5

   [group2]
   172.31.44.1
   172.31.44.6
   172.31.44.2
   ```

3. Save and exit the file.

### 5. Test Ad-Hoc Ansible Commands

You can use Ansible ad-hoc commands to quickly run tasks on the target servers.

1. To run a simple shell command (e.g., creating a file) on all servers:
   ```bash
   ansible -i inventory all -m shell -a "touch /tmp/devopsclass"
   ```

2. To run the same task on a specific group of servers (e.g., `group1`):
   ```bash
   ansible -i inventory group1 -m shell -a "mkdir /tmp/test1"
   ```

### 6. Create Your First Ansible Playbook

Ansible playbooks are YAML files that define a set of tasks to be executed on managed nodes.

1. Create a file called `firstplaybook.yml`:
   ```bash
   vim firstplaybook.yml
   ```

2. Define the playbook contents:
   ```yaml
   ---
   - name: Install Nginx
     hosts: all
     become: true  # This grants root privileges to the tasks

     tasks:
       - name: Install Nginx
         apt:
           name: nginx
           state: present

       - name: Start Nginx
         service:
           name: nginx
           state: started
   ```

   - **`hosts: all`**: This defines that the tasks will be executed on all the hosts specified in the inventory.
   - **`become: true`**: This gives root privileges to run tasks (required for installing and starting services).
   - **`tasks`**: Defines a list of tasks to be executed. In this case, the tasks install Nginx using `apt` and then start the Nginx service.

3. Save and close the file.

### 7. Run the Playbook

Now, you can execute the playbook to install and start Nginx on your target nodes.

1. Run the playbook:
   ```bash
   ansible-playbook -i inventory firstplaybook.yml
   ```

2. This will automatically:
   - Install Nginx.
   - Start the Nginx service on all the target VMs specified in the inventory.

### 8. Verify Nginx Installation

After the playbook runs, verify that Nginx is installed and running on the target servers.

1. Check if Nginx is running:
   ```bash
   sudo systemctl status nginx
   ```

2. Visit the IP address of the target VM(s) in a web browser. You should see the Nginx default welcome page.

---

## Conclusion

In this guide, you've learned how to:

- Set up an Ansible control node and target node.
- Generate and use SSH keys for passwordless login.
- Create and use an Ansible inventory file.
- Run ad-hoc Ansible commands for quick tasks.
- Create and execute a basic Ansible playbook to install and start Nginx.

For more information, refer to the [Ansible documentation](https://docs.ansible.com/).