# Ansible role for building and running squid.
=================================================

**summary**

This role is for building and running squid (the docker container version). 
Look in the tasks directory for each configuration.
All configurations are held atomically via their own files.

## Necessary preparations
A docker runtime environment must exist on the system.
Or use: https://github.com/conloos/ansible_role_docker

## SSL Certificates

                                                         ┌───────────────────┐
                                                         │   squid.service   │
                                                         │ Start/ Stop Squid │
                                                         │ Docker Container  │
                                                         └──────┬─────▲──────┘
                                                                │     │
   ┌─────────────────────────────────────────────────┐          │     │         ┌───────────────────────────┐
   │      squid-regenerate-certificate.service       │          │     │         │      squid-ssl.path       │
   │ Start Skript: "squid-regenerate-certificate.sh" ◄──────────┘     │         │ Monitor files for changes │
   └────────────────────────┬────────────────────────┘                │         │    and trigger actions    │
                            │                                         │         └─────────────┬─────────────┘
                            │                                         │                       │
           ┌────────────────▼─────────────────┐                       │                       │
           │ squid-regenerate-certificate.sh  │                       │                       │
           │   regenerate additional certs    │                       │                       │
           │ and restart the docker container │                       │                       │
           └──────────────────────────────────┘                       │                       │
                                                                      │          ┌────────────▼────────────┐
                                                                      │          │    squid-ssl.service    │
                                                                      └──────────┤ (restart squid.service) │
                                                                                 └─────────────────────────┘




## Variables that have to be defined

| variable | description | mandantory |
| -------- | ----------- | ---------- |




# example playbook
```
---
- hosts: 
    - example.com
  become: true
  vars:
    cloudinit_fqdn: example.com
  vars_files:
    - group_vars/vault.yml
  tasks:
  roles:
    - ansible_role_squid
```