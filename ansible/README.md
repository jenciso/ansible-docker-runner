## README

Playbook to provision gitlab runners running in container

## Prereq 

Docker 19.03

## Deploy 

```shell
ansible-playbook main.yaml -i inventory -e gitlab_registration_token="my_pat_token"
```
