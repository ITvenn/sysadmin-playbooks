# Déploiement et sécurisation de SSH via Ansible

Ce playbook Ansible permet d'installer, configurer et sécuriser le service OpenSSH Server sur une ou plusieurs machines cibles à l'aide d'un fichier de configuration personnalisé.

---

## Structure des fichiers attendue
```sh
project/
├── inventory.ini
├── playbook.yml
└── sshd_config
```
- inventory.ini : Fichier d’inventaire Ansible contenant les hôtes cibles.
- playbook.yml : Le playbook principal.
- sshd_config : Fichier de configuration SSH personnalisé à déployer.

---

## Prérequis

- Ansible installé sur la machine de contrôle.
- Accès SSH avec privilèges sudo sur les hôtes cibles.
- Clé SSH configurée ou authentification par mot de passe fonctionnelle.

---

## Utilisation

1. Définir les hôtes dans inventory.ini
```sh
[ssh_targets]
serveur1 ansible_host=192.168.1.10 ansible_user=admin
serveur2 ansible_host=192.168.1.11 ansible_user=admin
```

2. Exécuter le playbook
```sh
ansible-playbook -i inventory.ini playbook.yml -l ssh_targets
```
> Si vos hôtes demandent un mot de passe sudo, ajoutez l'option : --ask-become-pass

---

## Ce que fait le playbook

- Installe le paquet openssh-server via APT.
- Active et démarre le service ssh.
- Déploie un fichier sshd_config personnalisé vers /etc/ssh/sshd_config.
- Redémarre le service ssh si la configuration est modifiée.

---

## Remarques importantes

- Le fichier sshd_config doit être valide. Une erreur de syntaxe pourrait empêcher toute future connexion SSH.
- Avant toute modification en production, assurez-vous d'avoir un accès de secours (console, IPMI, KVM, etc.).
- Vous pouvez tester la validité du fichier avant de le déployer avec :
```sh
  sshd -t -f sshd_config
```
---

## Exemple de test post-déploiement
```sh
ssh admin@192.168.1.10
```
---

## Exemple de contenu playbook.yml

---
```sh
- name: Installation et configuration de SSH
  hosts: undefined_group
  become: yes
  tasks:

    - name: Installer le paquet openssh-server
      apt:
        name: openssh-server
        state: present
        update_cache: yes

    - name: Assure que le service SSH est activé et démarré
      systemd:
        name: ssh
        enabled: yes
        state: started

    - name: Déployer un fichier de configuration sshd personnalisé
      copy:
        src: sshd_config
        dest: /etc/ssh/sshd_config
        owner: root
        group: root
        mode: '0644'
      notify: Redémarrer SSH

  handlers:
    - name: Redémarrer SSH
      systemd:
        name: ssh
        state: restarted
```
---

## Exemple minimal de inventory.ini
```sh
[ssh_targets]
serveur1 ansible_host=192.168.1.10 ansible_user=admin
```
---

