# 🚀 Playbook Ansible - Mise à jour automatique de GLPI

Ce playbook permet de mettre à jour une instance **GLPI** vers une version spécifiée en ligne de commande tout en sauvegardant les répertoires critiques et la base de données.

---

## 📁 Arborescence attendue

```
glpi_upgrade/
├── inventory.ini
├── update_glpi.yml
└── README.md
```

---

## 📦 Fonctionnalités

- Validation de sécurité (répertoires existants)
- Sauvegarde :
  - Base de données MySQL
  - Répertoires `plugins`, `marketplace`
  - Fichier `downstream.php`
- Téléchargement de la version depuis GitHub
- Décompression et installation propre
- Restauration des fichiers critiques
- Permissions et redémarrage d'Apache
- Nettoyage automatique du script

---

## 🧾 Utilisation

### 1. Créez un inventaire

```ini
[glpi_servers]
192.168.1.50 ansible_user=admin
```

### 2. Lancez le playbook

```bash
ansible-playbook -i inventory.ini update_glpi.yml -e "glpi_version=10.0.10 db_name=glpidb db_password=motdepasse" --ask-pass
```

> Remplacez `glpi_version`, `db_name` et `db_password` par vos valeurs.

---

## ⚠️ Prérequis

- GLPI installé dans `/var/www/html/glpi`
- Apache2 en service
- Droits sudo nécessaires
- Variables passées en extra-vars obligatoires

---

## 🔐 Bonnes pratiques

- Lancez ce playbook pendant une période de maintenance.
- Sauvegardez vos snapshots système en parallèle.
- Testez en environnement de préproduction avant mise en production.

---

## 📎 Exemple complet

```bash
ansible-playbook -i inventory.ini update_glpi.yml \
  -e "glpi_version=10.0.10 db_name=glpidb db_password=SuperSecret123"
```

---

