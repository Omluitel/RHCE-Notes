## Guide to Sending Email Notifications with Ansible

Step 1: [Activate two-step verification](https://support.google.com/accounts/answer/185839) for your Gmail account.

Step 2: Generate and utilize [app-specific passwords](https://support.google.com/accounts/answer/185833) for Gmail.

Step 3: Navigate to your Gmail account's [security settings](https://myaccount.google.com/security) and locate "App passwords."

Step 4: Provide device information and generate a 16-digit app-specific password.

Step 5: Retrieve your 16-digit app-specific password.

Step 6: Encrypt the password using the following Ansible command:

```bash
ansible-vault encrypt_string YOUR_PASSWORD_HERE --name gmail_pass
```

Prepare your playbook and include the encrypted `gmail_pass` for authentication.

```yaml
---
- name: Send Email Notification
  hosts: localhost
  vars:
    gmail_username: "your_email@gmail.com"  # Your Gmail email address
    gmail_app_password: !vault |
      $ANSIBLE_VAULT;1.1;AES256
      66303234313637306437383164396435383732383266623737386366613731396439626539393665
      3865366330333037613636353931326236616665306433320a316530393937616563663565353063
      61396466633362333239323362373466323238323566313865613438343462373963333661633130
      3935333233306631350a376530653261653137633061613235323937366234653133653636383131
      3963
  tasks:
    - name: Send Email Notification
      mail:
        host: smtp.gmail.com
        port: 587
        username: "{{ gmail_username }}"
        password: "{{ gmail_app_password }}"
        to: "recipient@example.com"  # Email address of the recipient
        subject: "Test Email Notification"
        body: "This is a test email notification sent using Ansible."
        tls: yes
```

Replace `"your_email@gmail.com"` with your Gmail email address and `"recipient@example.com"` with the recipient's email address. Ensure you've replaced `"YOUR_ENCRYPTED_PASSWORD"` with the encrypted password generated using the `ansible-vault encrypt_string` command.