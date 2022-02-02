# TP 3

## Mon inventory

```yml
all:
  vars:
    #on configure notre user
    ansible_user: centos
    #on configure notre chemin vers notre clef privé ssh
    ansible_ssh_private_key_file: .ssh/id_rsa
  children:
    prod:
      hosts: 
        florent.monnet.takima.cloud:
```

## Mon playbook

```yml
#lance le playbook pour tous les hosts(centos@florent.monnet.takima.cloud)
- hosts: all
  gather_facts: false
  #lance en super user
  become: yes
  roles:
  #lance chaque role pour installer docker, créer le réseau, lancer les containers docker
    - install-docker
    - create-network
    - launch-database
    - launch-app
    - launch-front
    - launch-proxy
```

## Exemple d'un role pour lancer le container de la base de données

```yml
---
# tasks file for roles/launch-database
- name: launch postgres container
  docker_container:
    name: postgres-app
    # on utilise l'image sur docker hub
    image: florentmonnet2/postgres-image:1.1
    #on monte notre volume
    volumes:
      - /tmp/data
    #on relie la bdd à notre réseau
    networks: 
      - name: app-network
```
