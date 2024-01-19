Simple TLS certificates generation for nginx. Just feed a initial config in and let certbot takes care of the rest

Sample playbook:

```yaml
---
- name: to generate TLS certificate
  hosts: all
  roles:
    - role: nginx-certbot
      vars:
        nginx_certbot_notify_email: "youremail@gmail.com"
        nginx_certbot_server_names:
          - "yourdomain.com"