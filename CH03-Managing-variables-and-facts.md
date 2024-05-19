# Managing Variables and Facts

## Goal
Write playbooks that use variables to simplify management of the playbook and facts to reference information about managed hosts.

## Objectives
• Create and reference variables that affect particular hosts or host groups, the play, or the global environment, and describe how variable precedence works.

• Encrypt sensitive variables using Ansible Vault, and run playbooks that reference Vault-encrypted variable files.

• Reference data about managed hosts using Ansible facts, and configure custom facts on managed hosts.



## LAB
### LAB01
#### Create user using variables in playbook user01.yml

``` bash
   ---        
     - name: creating user using variables
       hosts:  all
       vars:  
         user: om
       tasks: 
         - name: Create user
           ansible.builtin.user:
            name: "{{ user  }}"
            state:  absent
             
```

##### Dry run 
```bash
ansible-navigator run -m stdout --check user01.yml
```
##### Run playbook

```bash
ansible-navigator run -m stdout user01.yml
```

##### Getting helps from ansible-navigator doc
```bash
ansible-navigator doc user
```

### LAB02
#### Write message using variable and debug

```bash       
---
   - name: Print Welcome Message
     hosts: all
     vars:
       my_variable: welcome!
    
     tasks:
       - name: Display the Welcome Message
         ansible.builtin.debug:
            msg: "{{ my_variable }}"

...

```
### LAB03
#### Installed multiple packages using variable

```bash
---
- name: Installed packages
  hosts:  all
  vars:
    pack:
        -   vsftpd
        -   telnet    
  tasks:
  - name: install different packages
    ansible.builtin.dnf:
      name: "{{ pack }}"
      state:  present
...
```

### LAB04
#### Pass variables using .yml file
##### define vars directory and create variable.yml file 
```bash
pack:   vsftpd,telnet
```
##### write yml file and pull variable.yml
```bash
---
- name: pull variable using .yml file
  hosts:  all
  vars_files: vars/variables.yml
  tasks:
    - name: Ensure package is installed
      ansible.builtin.yum:
          name: "{{  pack  }}"
          state:  present
```

### LAB05
#### pass variable using inventory file

```bash
[web]
192.168.1.115 my_var=om
```
#### which value would ansible pick if we also define this variable on .yml playbook
```bash
---
- name: Testing variable
  hosts:  web
  vars:
    my_var: Ram
  tasks:
    - name: show the my variable: {{ my_var }}
      ansible.builtin.yum:
        name: {{my_var}}
        state:  present

```

##### Outcomes: Play will pick the variable which is defined on playbook.


### What is register variable
### LAB06

In Ansible, a "register" variable is used to capture the output of a task and store it in a variable for later use within the playbook. This is particularly useful when you need to extract specific information from a task's result or when you want to reuse the result in subsequent tasks.

```bash
- name: play1
  hosts:  all
  tasks: 
  - name: Install a package
    ansible.builtin.yum:
      name: httpd
      state: present
    register: package_result

  - name: Print package installation result
    ansible.builtin.debug:
      var: package_result
```

### LAB07
```bash
---
- name: play1
  hosts:  all
  tasks:
    - name: check the remote machine hostname
      command:  hostname
      register: yahoo
    - name: print the output
      ansible.builtin.debug:
        msg:  "remote hostname is {{  yahoo.stdout  }}"  # check out put of yahoo and also know how you can get particular value
```

### LAB08 - loop is used here
```yaml
---
- name: play 1
  hosts: all
  tasks:
    - name: show free space
      ansible.builtin.shell: df -Th /
      register: first

    - name: show memory space
      ansible.builtin.shell: free -m
      register: second

    - name: print output
      ansible.builtin.debug:
        msg: "{{ item }}"
      loop:
        - "{{ first.stdout_lines }}"
        - "{{ second.stdout_lines }}"
```

## Ansible Vault
Ansible Vault securely encrypts sensitive data like passwords and keys in your Ansible scripts. You can encrypt entire files or specific variables, and use simple commands to encrypt, decrypt, or edit these files. When running playbooks, provide the decryption password to access the encrypted data. This ensures only authorized users can read the protected information.

### creating encrypted file
To create:

```bash
ansible-vault create first1.yml
```
To view:

```bash
ansible-vault view file1.yml
```
To Edit:

```bash
ansible-vault file1.yml
```
To encrypt file which already exist:
```bash 
ansible-vault encrypt file2.yml
```
To change password
```bash
ansible-vault rekey file1.yml
```
Save vault password to a file and call that password file when running .yml file:

```bash
ansible-navigator run -m stdout --vault-password-file=mypass.txt file1.yml # where mypass.txt has password saved
```
Syntax check:
```bash
ansible-navigator run -m stdout --vault-password-file=mypass.txt --syntax-check file1.yml
```
### Get help on ansible-navigator
```bash
ansible-navigator --help
ansible-navigator run --help-playbook -m stdout
```
## Managing Facts

### What is ansible facts:
Ansible facts are automatically collected details about remote hosts, such as system, network, hardware, and environment information. These facts are gathered using the setup module at the start of a playbook run.

Turn off gathering facts:

```bash
---
- name: play1
  hosts:  all
  gather_facts: no
  tasks:
  - name: install httpd
    ansible.builtin.yum:
      name: httpd
      state:  present
```
See facts of hosts:
```bash
ansible -m setup  all
```
Filter data;
```bash
ansible -m setup all -a 'filter=ansible_all_ipv4_addresses'
```

How do we call facts:
```bash
ansible_facts['hostname']
ansible_facts['fqdn']
```
```bash
---
- name: play1
  hosts: all
  tasks:
    - name: show the hostname
      ansible.builtin.debug:
        msg: "Machine Hostname is {{ ansible_facts['fqdn'] }}" # calling facts
```

Q1: find how you can turn off gathering facts for specific hosts ?
Q2: Setting up customs facts on hosts ?


### Magic Variables in Ansible:
Magic variables in Ansible are special variables that are automatically available to playbooks and tasks without the need for explicit definition. They provide important information about the environment, playbook execution, and inventory.

```bash
---
- name: Demonstrate Magic Variables
  hosts: all
  gather_facts: yes
  tasks:
    - name: Show the fully qualified domain name
      ansible.builtin.debug:
        msg: "Machine Hostname is {{ ansible_facts['fqdn'] }}"

    - name: Show the inventory hostname
      ansible.builtin.debug:
        msg: "Inventory hostname is {{ inventory_hostname }}"

    - name: Show the short inventory hostname
      ansible.builtin.debug:
        msg: "Short inventory hostname is {{ inventory_hostname_short }}"

    - name: List all hosts in the current play
      ansible.builtin.debug:
        msg: "Hosts in this play are: {{ play_hosts }}"

    - name: List all hosts in the webservers group
      ansible.builtin.debug:
        msg: "Webservers group contains: {{ groups['webservers'] | default([]) }}"

    - name: Display the directory of the playbook
      ansible.builtin.debug:
        msg: "This playbook is located at {{ playbook_dir }}"

    - name: Display the Ansible version
      ansible.builtin.debug:
        msg: "Ansible version is {{ ansible_version.full }}"
```



