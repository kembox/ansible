To install [mox](https://github.com/mjl-/mox) - a modern full-fleged email server for personal usage - where we have already webserver listening to 80 and 443.

Mox is cool. A single binary for quickly spinning an email server without throwing too much effort to postfix, dovecot, roundcube and the like. 
It also comes with a handy `quickstart` command that setup almost everything for us and leave us the last steps to update our DNS records only ( with clear instructions ). 
Though, it greats when it stands alone only. To run it under a reverse proxy, we need to:
- Generate certificates ourselves
- Adjust mox.conf to points to correct certs's location
- Configure nginx to point to new local port that mox's listening to ( 127.0.0.1:81 )

Beside that,`quickstart` is very handy but it's not easy to reuse. Everytime it runs it creates new configs, DKIM keys, passwords that requires changes from our side as well. ( Not to blame, it's designed for a quick initialization )
This role aims to solve the problems above: to automate the certs and nginx setup, to be idempotent execution so we can update small details easily without replacing old configs. 

Sample playbook:
```yaml
---
- name: Setup mox as email server
  hosts: your_host 
  vars_files: 
    - ../ansible-playbooks/var_files/secrets/mox.yaml
  vars:
    mox_domain: "yourdomain.com"
    mox_mail_hostname: "mail.yourdomain.com"
    mox_public_ip: "x.x.x.x"
    mox_default_account: "you"
    mox_mta_sts_policy_version_id: "20232323062646"

  roles: 
    - role: nginx-certbot 
      vars:
        nginx_certbot_server_name: "autoconfig.{{ mox_domain }}"
        nginx_certbot_custom_sample: "mox_mailserver"

    - role: nginx-certbot 
      vars:
        nginx_certbot_server_name: "mta-sts.{{ mox_domain }}"
        nginx_certbot_custom_sample: "mox_mailserver"
    - role: nginx-certbot 
      vars:
        nginx_certbot_server_name: "{{ mox_mail_hostname }}"
        nginx_certbot_custom_sample: "mox_mailserver"
    #I don't know how to loop 1 role through a list of var :| 

    - role: mox
      tags: mox
```