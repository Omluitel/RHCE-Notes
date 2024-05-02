1. **Using Ansible Development Tools (ADT)**:
   - The recommended approach is to use the **ansible-dev-tools** package, which streamlines the setup and usage of several tools needed for creating Ansible content.
   - Here are the steps:
     - Install the desired container engine (either **podman** or **Docker**) for execution environment support.
     - Install the python package manager using the system package installer:
       ```bash
       sudo dnf install python3-pip
       ```
     - Install Ansible Navigator:
       ```bash
       python3 -m pip install ansible-navigator --user
       ```
     - Add the installation path to your user shell initialization file:
       ```bash
       echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.profile
       ```
     - Refresh the PATH:
       ```bash
       source ~/.profile
       ```
     - Launch Ansible Navigator:
       ```bash
       ansible-navigator
       ```

2. **Using EPEL Repository**:
   - If you're using RHEL 9, you can follow these steps:
     - First, ensure that your system is up-to-date:
       ```bash
       sudo dnf update
       ```
     - Install the EPEL (Extra Packages for Enterprise Linux) repository:
       ```bash
       sudo dnf install epel-release
       ```
     - Next, install Ansible Navigator:
       ```bash
       sudo dnf install ansible-navigator
       ```
     - Launch Ansible Navigator when the installation is complete.

3. **Red Hat Ansible Automation Platform (Early Access)**:
    - Attach the Red Hat Ansible Automation Platform SKU:
        ```bash
        subscription-manager attach --pool=<sku-pool-id>
        ```
    - Enable the repository for RHEL 8:
        ```bash
        sudo subscription-manager repos --enable ansible-automation-platform-2.0-early-access-for-rhel-8-x86_64-rpms
        ```
    - Install Automation content navigator:
        ```bash
        dnf install ansible-navigator
       ```


[Ansible Navigator Installation Documentation](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.0-ea/html/ansible_navigator_creator_guide/assembly-installing_on_rhel_ansible-navigator)

[What is Ansible Navigator and How to Install at RHEL 9](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.0-ea/html/ansible_navigator_creator_guide/assembly-installing_on_rhel_ansible-navigator)
