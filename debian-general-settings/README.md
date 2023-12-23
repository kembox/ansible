My starter pack to start working with a debian server: packages to install, sysctl settings to tweak and so on

Sample playbook:

```
---
- name: General settings
  hosts: your_host 
  roles:
    - role: your_role_dir/debian-general-settings
```