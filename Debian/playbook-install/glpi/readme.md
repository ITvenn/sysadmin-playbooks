# 📦 Playbook Ansible - Installation automatique de GLPI

## 🔧 Objectif

Ce playbook permet l'installation automatisée de **GLPI** sur un serveur **Debian**, incluant :

- Apache2
- PHP 8.2 avec modules requis
- MariaDB
- Téléchargement et configuration de GLPI
- Configuration des droits et répertoires
- Fichier `downstream.php` et `local_define.php`

---

## 🚀 Prérequis

- Système : Debian 11 ou supérieur
- Accès root ou équivalent (via `become: true`)
- Ansible installé sur le poste de contrôle
- Serveur cible disponible via SSH

---

## 📁 Fichiers fournis

- `install_glpi.yml` : le playbook principal
- Ce `README.md`

---

## 📌 Variables à définir

Certaines variables doivent être spécifiées au lancement du playbook :

| Variable           | Description                                     | Exemple         |
|--------------------|-------------------------------------------------|-----------------|
| `glpi_version`     | Numéro de version GLPI à installer              | `X.X.X`       |
| `ip_server`        | Adresse IP publique ou privée du serveur cible  | `X.X.X.X`  |
| `mysql_root_pass`  | Mot de passe souhaité pour l'utilisateur root   | `motdepasse` |
| `install_php_ldap` | Installer le support LDAP (true/false)          | `true`          |

---

## 📦 Lancement du playbook

```bash
ansible-playbook install_glpi.yml -i <target>, \
  -e glpi_version=10.0.14 \
  -e ip_server=192.168.1.10 \
  -e mysql_root_pass='SuperSecret42' \
  -e install_php_ldap=true
```
Remplacez <target> par le nom ou l’adresse IP de votre serveur cible. Le , à la fin est obligatoire si vous utilisez un inventaire en ligne de commande.

## 🧩 Structure des tâches
Le playbook exécute les étapes suivantes :
- Mise à jour des paquets APT
- Installation d’Apache2 et configuration de VirtualHost
- Installation de PHP 8.2 et des modules nécessaires
- Installation de MariaDB et sécurisation de l’accès root
- Téléchargement et extraction de GLPI
- Déplacement et sécurisation des répertoires files, config, marketplace, etc.
- Création des fichiers de configuration
- Redémarrage des services Apache2 et PHP-FPM

## 📎 Informations supplémentaires
L'installation crée les répertoires suivants :

/etc/glpi pour la configuration

/var/lib/glpi pour les fichiers dynamiques

/var/log/glpi pour les logs

L’interface GLPI sera accessible à l’adresse :
```bash
👉 http://<ip_server>
Identifiants par défaut : glpi / glpi
```

## 🛡️ Sécurité
Le script supprime automatiquement le fichier install.php après installation

Le mot de passe root MySQL est défini via Ansible

## 📝 Auteur
HAMEL Vincent
Administrateur Systèmes & Réseaux