To install [mox](https://github.com/mjl-/mox) - a modern full-fleged email server for personal usage - where we have already webserver listening to 80 and 443

Sample playbook:
```yaml
---
- name: Setup mox as email server
  hosts: mox
  vars_files: 
    - ../ansible-playbooks/var_files/secrets/mox.yaml
  vars:
    mox_domain: "dbgsym.dev"
    mox_mail_hostname: "mail.dbgsym.dev"
    mox_public_ip: "103.138.113.144"
    mox_default_account: "tungdam"
    mox_mta_sts_policy_version_id: "20231101T062646"

  roles: 
    - role: nginx-certbot 
      vars:
        nginx_server_name: "autoconfig.{{ mox_domain }}"
    - role: nginx-certbot 
      vars:
        nginx_server_name: "mta-sts.{{ mox_domain }}"
    - role: nginx-certbot 
      vars:
        nginx_server_name: "{{ mox_mail_hostname }}"
    #I don't know how to loop 1 role through a list of var :| 

    - role: mox
      tags: mox
```