# Déploiement d'un serveur powerDNS dans des conteneurs docker.

## Objectif du projet
* Ce projet vise à déployer le service powerdns dans des conteneurs docker.

* Le rôle ansible ont été créés pour gérer ce déploiement
    - [powerdns](https://github.com/khuliam/powerdns.git) : permet de déployer le service powerdns.


## Les prérequis
Le déploiement se fait sur un serveur spécifique et utilise des outils IaC. On aura besoin des outils/serveurs suivants : 
- Un serveur `Jenkins` : sur lequel seront créés et exécutés nos pipelines.
- Un serveur `Ansible` : minimum la version 2.10.17. C'est sur ce serveur que seront lancés nos playbooks Ansible. Il faudra installer la community.general d'Ansible (`ansible-galaxy collection install community.general`).
- Un serveur `powerdns`: Une machine virtuelle `Debian 12` sur laquelle est installé les outils suivants :
    - Docker
    - Docker-compose
    - Les modules Python :
        - docker
        - docker-compose

## Les variables du pipeline
* Les configurations sont faites via un `pipeline Jenkins` prenant en compte un ensemble de paramètres :

    - `SERVER_HOSTNAME` : le hostname du serveur powerdns. (Exemple : pdns.dev.kdh.ca)
    - `SERVER_USER` : l'utilisateur de connexion au serveur powerdns
    - `SERVER_PASSWORD` : Lle mot de passe de connexion au serveur powerdns
    - `ANSIBLE_VM_HOSTNAME` : le hostname du serveur Ansible (Exemple : ansible.dev.kdh.ca)
    - `ANSIBLE_VM_USER` : l'utilisateur de connexion au serveur Ansible
    - `ANSIBLE_VM_PASSWORD` : Le mot de passe de connexion au serveur Ansible
    - `ENV`: l'environnement de déploiement (dev, rc ou prod)
    - `POWERDNS_DOMAINE` : le domaine pour le serveur powerdns (Exemple: dev.kdh.ca)
    - `PDNS_SERVER_IP`: l'adresse IP de votre serveur pdns

* Toutes ces variables sont prises en compte dans un `Jenkinsfile` que Jenkins lira et interprétera.

## Les images docker utilisées pour les conteneurs:
- [mariadb:latest](https://hub.docker.com/_/mariadb)
- [pschiffe/pdns-mysql:latest](https://hub.docker.com/r/pschiffe/pdns-mysql)
- [powerdnsadmin/pda-legacy](https://hub.docker.com/r/powerdnsadmin/pda-legacy)
