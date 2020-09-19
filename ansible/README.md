## README

Playbook to provision gitlab runners running in container

## Prereq 

- 2 VMs running docker 19.03 
  - 1 VM to use as runner build server
  - 1 VM to use as runner deploy server

## Deploy 

```shell
ansible-playbook main.yaml -i inventory -e gitlab_registration_token="my_pat_token"
```
