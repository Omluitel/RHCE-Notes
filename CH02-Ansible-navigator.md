

## Ansible Inventory File

An inventory file in Ansible is a configuration file that defines the hosts and groups of hosts on which Ansible commands will be executed. It helps Ansible to determine the target machines for running tasks or playbooks.


## Inventory File Example


```ini
# Individual host
host4

# Web servers group
[web_servers]
host1
host2

# Database servers group
[db_servers]
host3

# Web servers with range
[web_servers]
server[1:5].example.com

# Database servers with range
[db_servers]
db-server-[01:03].example.com

# Parent group including child groups
[Servers:children]
db_servers   # Child group containing database servers
web_servers  # Child group containing web servers

```


## Ansible Inventory Commands

To generate a list of hosts and groups from the Ansible inventory file and output the list to the standard output (stdout), run the following command in the current directory:

```bash
ansible-navigator inventory -i inventory -m stdout --list
```
To view hosts in a specific group (e.g., web_servers), run the following command:
```bash
ansible-navigator inventory -i inventory -m stdout --graph web_servers
```
To check details of ungroupded hosts, run the following command:
```bash
ansible-navigator inventory -i inventory -m stdout --graph ungrouped
```

To check details for a particular host (e.g., host1), run the following command:
```bash
ansible-navigator inventory -i inventory -m stdout --host host1
```


## What is ansible.cfg and its importance

`ansible.cfg` is a configuration file used by Ansible, an automation tool widely employed in IT infrastructure management, application deployment, and various other tasks. This file holds a multitude of settings and options that dictate how Ansible behaves when executing tasks, managing inventory, handling errors, and interacting with remote systems.

The importance of `ansible.cfg` lies in its ability to:

1. **Centralize Configuration**: Instead of specifying configurations repeatedly across different Ansible commands or playbooks, `ansible.cfg` allows users to define settings once in a centralized location.

2. **Ensure Consistency**: By enforcing consistent configurations across Ansible projects, `ansible.cfg` mitigates the risk of errors stemming from inconsistencies in settings. This consistency enhances reliability and reproducibility in automation workflows.

3. **Customize Ansible Behavior**: Users can tailor Ansible's behavior to suit their specific needs and preferences by adjusting settings such as verbosity levels, default inventory locations, module paths, connection parameters, and more within `ansible.cfg`.

4. **Override Default Settings**: `ansible.cfg` empowers users to override default settings provided by Ansible, enabling fine-grained control over various aspects of Ansible's operation.

5. **Simplify Command Line Usage**: By encapsulating configurations in a file, `ansible.cfg` simplifies command-line usage by reducing the need for verbose command-line arguments. This makes Ansible commands more concise and easier to manage.

In summary, `ansible.cfg` serves as a cornerstone of Ansible configuration management, offering a centralized and flexible mechanism to customize and standardize Ansible behavior across projects and environments.

## Example of ansible.cfg file
```ini

[defaults]

# Path to the inventory file
inventory = ./inventory

# Default user for SSH connections
remote_user = admin

# Don't prompt for password
ask_pass = false


[privilege_escalation]

# Enable privilege escalation
become = True

# Specify the method for privilege escalation
become_method = sudo

# Specify the user for privilege escalation
become_user = root

# Don't prompt for privilege escalation password
become_ask_pass = false
```

## Setting Up Passwordless Authentication in Linux

### Generate SSH Key Pair

Open a terminal on your local machine and run the following command to generate a new SSH key pair:

```bash
ssh-keygen
```

### Copy Public Key to Remote Server

Once you've generated your SSH key pair, you need to copy the public key to the remote server. You can use the `ssh-copy-id` command:

```bash
ssh-copy-id username@remote_server
```

Replace `username` with your username on the remote server and `remote_server` with the hostname or IP address of the server. You'll be prompted to enter your password for the last time.

### Ensure SSH Configuration Allows Key-Based Authentication

On the remote server, ensure that SSH key-based authentication is allowed. Open the SSH server configuration file (`/etc/ssh/sshd_config`) and ensure the following settings are in place:

```plaintext
PubkeyAuthentication yes
PasswordAuthentication no
ChallengeResponseAuthentication no
```

### Restart SSH Service

After making changes to the SSH configuration, restart the SSH service to apply the changes:

```bash
sudo systemctl restart sshd
```

### Test SSH Connection

Now, try to SSH into the remote server again. You shouldn't be prompted for a password:

```bash
ssh username@remote_server
```

With these steps, you've set up passwordless authentication via SSH on your Linux system. Remember to keep your private SSH key secure, as it grants access to any server where the corresponding public key is added to the authorized keys list.

## Guide for Creating New File in sudoers.d Directory

1. **Open Terminal**: Open a terminal on your Linux system.

2. **Switch to Root**: Switch to the root user by running:
   ```bash
   sudo su -
   ```

3. **Navigate to sudoers.d Directory**: Change to the `/etc/sudoers.d/` directory. This directory is where additional sudo configuration files are stored:
   ```bash
   cd /etc/sudoers.d/
   ```

4. **Create New Configuration File**: Use your preferred text editor to create a new configuration file. For example, to create a file named `username` (replace `username` with the desired username):
   ```bash
   nano username
   ```

5. **Add Sudo Configuration**: Inside the file, add the following line to grant sudo permissions to the user without a password prompt:
   ```plaintext
   username ALL=(ALL) NOPASSWD:ALL
   ```
   Replace `username` with the username of the user you want to grant sudo permissions to.

6. **Save and Exit**: Save the file and exit the text editor. In nano, you can do this by pressing `Ctrl` + `X`, then `Y`, and finally `Enter`.

7. **Set File Permissions**: Ensure that the file has the correct permissions. It should be readable only by root:
   ```bash
   chmod 440 username
   ```

8. **Test sudo Access**: Test sudo access by switching to the user account:
   ```bash
   exit
   ```
   Then, try running a command with sudo without entering a password:
   ```bash
   sudo ls
   ```
   If everything is configured correctly, the command should execute without prompting for a password.

Using the sudoers.d directory to create separate configuration files provides a more modular and manageable way to configure sudo permissions, especially in environments with multiple users or complex sudo policies. Remember to always follow best practices and security guidelines when configuring sudo permissions.



## Managing Settings for Automation Content Navigator
You can create a configuration file (or settings file) for ansible-navigator to override the
default values of its configuration settings. The settings file can be in JSON (.json) or YAML
(.yml or .yaml) format. This discussion focuses on the YAML format.
Automation content navigator looks for a settings file in the following order and uses the first file
that it finds:

• If the ANSIBLE_NAVIGATOR_CONFIG environment variable is set, then use the configuration
file at the location it specifies.

• An ansible-navigator.yml file in your current Ansible project directory.

• A ~/.ansible-navigator.yml file (in your home directory). Notice that it has a "dot" at the
start of its file name.

Just like the Ansible configuration file, each project can have its own automation content navigator
settings file.
The following ansible-navigator.yml file configures some common settings

### Create ansible-navigator.yml file in the working directory

```bash
ansible-navigator:
  execution-environment:
    image: utility.lab.example.com/ee-supported-rhel8:latest
    pull:
      policy: missing
  playbook-artifact:
    enable: false
```


### Syntax check using Ansible Navigator
```bash
ansible-navigator run -m stdout --syntax-check install_file.yml
```

### Ansible helps 
```bash
ansible-navigator doc -l
```
```bash
ansible-navigator doc yum
```

### Running a playbook using Ansible Navigator
```bash
ansible-navigator run playbook.yml -m stdout -i inventory
```
