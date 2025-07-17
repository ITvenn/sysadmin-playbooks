# Mise à jour de la configuration SSH avec Ansible

Ce playbook Ansible déploie un fichier de configuration `sshd_config` personnalisé sur les serveurs cibles et redémarre le service SSH si nécessaire.

---

## Structure des fichiers attendue

```
project/
├── inventory.ini
├── update_sshd_config.yml
└── sshd_config
```

- `inventory.ini` : fichier d'inventaire avec vos hôtes.
- `update_sshd_config.yml` : playbook Ansible pour déployer la config SSH.
- `sshd_config` : fichier de configuration SSH personnalisé.

---

## Prérequis

- Ansible installé sur la machine de contrôle.
- Accès SSH avec privilèges sudo sur les hôtes cibles.

---

## Utilisation

1. Préparez votre `inventory.ini` :

```ini
[serveurapache]
192.168.1.10 ansible_user=admin

[serveurnginx]
192.168.1.11 ansible_user=admin
```

2. Placez votre fichier `sshd_config` personnalisé dans le dossier.

3. Exécutez le playbook :

```sh
ansible-playbook -i inventory.ini playbook.yml -l ssh_targets
```
> Si vos hôtes demandent un mot de passe sudo, ajoutez l'option : --ask-become-pass

---

## Contenu du playbook `update_sshd_config.yml`

```yaml
---
- name: Déployer la configuration sshd personnalisée
  hosts: undefined_group
  become: yes
  tasks:
    - name: Copier le fichier sshd_config personnalisé
      copy:
        src: sshd_config
        dest: /etc/ssh/sshd_config
        owner: root
        group: root
        mode: '0644'
      notify: Redémarrer le service SSH

  handlers:
    - name: Redémarrer le service SSH
      systemd:
        name: ssh
        state: restarted
```

---

## Remarques

- Veillez à ce que le fichier `sshd_config` soit valide pour éviter de perdre l'accès SSH.
- Avant tout déploiement en production, assurez-vous d'avoir un accès de secours (console, IPMI, etc.).

---

## Test post-déploiement

```bash
ssh admin@192.168.1.10
```

---

## Contact

Pour toute question, contactez l’administrateur système.
