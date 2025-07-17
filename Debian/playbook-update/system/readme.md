# 🛠️ Playbook Ansible - Mise à jour sécurisée de Debian

Ce playbook Ansible permet de réaliser une mise à jour complète d’un système Debian de manière sécurisée, en évitant toute exécution accidentelle grâce à un groupe de cible inexistant par défaut (`undefined_group`).

---

## 📁 Fichiers attendus

```
project/
├── inventory.ini
├── update_debian.yml
└── README.md
```

---

## ⚙️ Contenu du playbook `update_debian.yml`

```yaml
---
- name: Mise à jour sécurisée du système Debian
  hosts: undefined_group  # Groupe volontairement inexistant
  become: yes
  tasks:
    - name: Mettre à jour le cache des paquets
      apt:
        update_cache: yes

    - name: Simuler la mise à jour complète sans appliquer
      apt:
        upgrade: dist
        simulate: yes
      register: simulation_result

    - name: Afficher les paquets à mettre à jour
      debug:
        var: simulation_result

    - name: Appliquer la mise à jour si nécessaire
      apt:
        upgrade: dist
        autoremove: yes
        autoclean: yes
      when: simulation_result.changed

    - name: Redémarrer le système si nécessaire
      reboot:
        reboot_timeout: 300
      when: ansible_facts['needs_reboot'] | default(false)
```

---

## 🧪 Exécution sécurisée

### 1. Exemple d’inventaire `inventory.ini` :

```ini
[serveurapache]
192.168.1.10 ansible_user=admin

[serveurnginx]
192.168.1.11 ansible_user=admin
```

### 2. Exécution manuelle avec ciblage obligatoire :

```bash
ansible-playbook -i inventory.ini update_debian.yml -l serveurapache
```

> ✅ Le playbook ne fera **rien** si vous oubliez `-l` : cela protège contre les erreurs humaines.

---

## ✅ Bonnes pratiques

- **Testez en préproduction** avant de lancer en production.
- **Validez le résultat de la simulation (`simulate: yes`)**.
- Ayez un accès de secours (console KVM/IPMI) si le système ne revient pas après redémarrage.
- Gardez un snapshot ou une sauvegarde à jour avant mise à jour critique.

---

