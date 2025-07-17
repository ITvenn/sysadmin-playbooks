# 🔧 Playbook Ansible - Mise à jour automatique de Graylog

Ce playbook Ansible permet de **mettre à jour automatiquement Graylog** vers une version spécifiée, tout en garantissant sécurité, contrôle utilisateur, et maintien du service.

---

## 📁 Structure attendue

```
graylog_update/
├── inventory.ini
├── update_graylog.yml
└── README.md
```

---

## ⚙️ Fonctionnalités

- Arrêt propre du service Graylog
- Téléchargement dynamique de la version souhaitée
- Installation via le paquet `.deb` du dépôt Graylog
- Mise à jour du paquet `graylog-server`
- Application d’un `apt-mark hold` pour figer la version
- Redémarrage du service
- Confirmation manuelle via invite

---

## 🧪 Utilisation

### 1. Créez un inventaire `inventory.ini`

```ini
[graylog_servers]
192.168.1.100 ansible_user=admin
```

Adaptez l'adresse IP et l'utilisateur selon vos cibles.

### 2. Lancez le playbook

```bash
ansible-playbook -i inventory.ini update_graylog.yml
```

### 3. Répondez aux invites

- Entrez la version Graylog souhaitée (ex: `5.2`)
- Confirmez la mise à jour avec `o` (tout autre caractère annule le processus)

---

## 🔒 Sécurité & Bonnes pratiques

- **Pas de version en dur** : vous la renseignez au moment du lancement.
- **Aucune modification involontaire** : confirmation manuelle requise.
- **Rollback facilité** si vous utilisez des snapshots ou des containers.
- **Répertoire temporaire** utilisé : `/tmp` pour éviter d'encombrer le système.

---

## 🧼 Nettoyage

Le fichier `.deb` est supprimé automatiquement après installation. Le paquet est également "gelé" via :

```bash
apt-mark hold graylog-server
```

---
