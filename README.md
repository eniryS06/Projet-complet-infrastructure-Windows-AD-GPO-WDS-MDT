# Infrastructure Windows Entreprise – AD DS, DNS, DFS, GPO, WDS, MDT

[![Windows Server](https://img.shields.io/badge/Windows_Server-2022-0078D6)](https://www.microsoft.com/windows-server)
[![Active Directory](https://img.shields.io/badge/Active_Directory-DS-00A4EF)](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/ad-ds-getting-started)
[![DFS](https://img.shields.io/badge/DFS-Replication-2E8B57)](https://docs.microsoft.com/en-us/windows-server/storage/dfs-replication/dfsr-overview)
[![GPO](https://img.shields.io/badge/GPO-Group_Policy-FF8C00)](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-security-baselines)
[![WDS](https://img.shields.io/badge/WDS-Deployment-8A2BE2)](https://docs.microsoft.com/en-us/windows-server/administration/windows-deployment-services/windows-deployment-services-overview)
[![MDT](https://img.shields.io/badge/MDT-Microsoft_Deployment_Toolkit-2E8B57)](https://docs.microsoft.com/en-us/windows/deployment/deploy-windows-mdt/get-started-with-the-microsoft-deployment-toolkit)

> Projet d'infrastructure Windows complète : Active Directory, DNS, DFS, GPO, WDS et MDT.  
> Mise en place d'une architecture d'entreprise avec déploiement automatisé et sécurisé.

---

## Table des matières

- [Architecture globale](#-architecture-globale)
- [Active Directory & DNS](#-active-directory--dns)
- [DFS - Distributed File System](#-dfs---distributed-file-system)
- [GPO - Group Policy Objects](#-gpo---group-policy-objects)
  - [Liste des GPO déployées](#liste-des-gpo-déployées)
  - [Vérification des GPO](#vérification-des-gpo)
- [WDS - Windows Deployment Services](#-wds---windows-deployment-services)
- [MDT - Microsoft Deployment Toolkit](#-mdt---microsoft-deployment-toolkit)
- [Problèmes rencontrés](#-problèmes-rencontrés)
- [Structure du dépôt](#-structure-du-dépôt)
- [Ressources](#-ressources)

---

## Architecture globale

| Rôle | Serveur | IP | Description |
|------|---------|-----|-------------|
| Contrôleur de domaine 1 | DC1 (GUI) | 192.168.1.101 | AD DS, DNS, DFS, GPO |
| Contrôleur de domaine 2 | DC2 (Core) | 192.168.1.102 | Réplication AD, redondance |
| WDS + MDT | SRV-DEPLOY | 192.168.1.103 | Déploiement réseau et PXE |

---

## Active Directory & DNS

### Installation du premier contrôleur de domaine (DC1)

![DC1 Installation](screenshots/01-dc1-installation.png)

### Installation du deuxième contrôleur (DC2 - Mode Core)

![DC2 Core Installation](screenshots/02-dc2-core-installation.png)

### Configuration DNS

![Configuration DNS](screenshots/03-dns.png)

**Configuration :**

| Élément | Détail |
|---------|--------|
| Nom du domaine | `holding.local` |
| DC1 | Interface graphique (GUI) |
| DC2 | Mode Core (sans interface) |
| DNS | Intégré à l'Active Directory |
| Réplication | DC1 → DC2 automatique |

---

## DFS - Distributed File System

### Installation de DFS

![DFS Installation](screenshots/04-dfs-installation.png)

### Configuration des partages

![Partages DFS](screenshots/06-dfs-replication.png)

### Réplication DFS

![Réplication DFS](screenshots/02-DFS/06-dfs-replication.png)

**Configuration :**

| Élément | Détail |
|---------|--------|
| Namespace | `\\holding.local\DFS` |
| Dossiers répliqués | Documents, Applications, Données |
| Topologie | Hub and spoke |
| Planification | Réplication continue |

---

## GPO - Group Policy Objects

### Liste des GPO déployées

| # | GPO | Effet | Statut |
|---|-----|-------|--------|
| 1 | **Désactivation CMD** | Bloque l'invite de commande | ✅ |
| 2 | **Désactivation Registre** | Bloque l'accès à regedit | ✅ |
| 3 | **Désactivation Windows Update** | Désactive les mises à jour auto | ✅ |
| 4 | **Fond d'écran personnalisé** | Applique un wallpaper corporate | ✅ |
| 5 | **Désactivation Pare-feu** | Désactive Windows Firewall | ✅ |
| 6 | **Installation logiciels** | Déploiement 7-Zip + Firefox | ✅ |

### Captures des GPO

| GPO | Capture |
|-----|---------|
| Désactivation CMD | ![Disable CMD](screenshots/07-gpo-cmd-disable.png) |
| Désactivation Registre | ![Disable Registry](screenshots/03-GPO/08-gpo-registry-disable.png) |
| Désactivation Windows Update | ![Disable Updates](screenshots/03-GPO/09-gpo-windows-update-disable.png) |
| Fond d'écran | ![Wallpaper](screenshots/10-gpo-wallpaper.png) |
| Désactivation Pare-feu | ![Disable Firewall](screenshots/11-gpo-firewall-disable.png) |
| Déploiement 7-Zip + Firefox | ![Software Deployment](screenshots/12-gpo-7zip-firefox.png) |

### Vérification des GPO

#### Résultat de `gpresult /h`

![GPResult](screenshots/03-GPO/13-gpo-gpresult.png)

#### Détails des GPO appliquées

![GPO Details](screenshots/03-GPO/14-gpo-details.png)

**Commandes utiles :**


# Forcer la mise à jour des GPO
```powershell
gpupdate /force
```

# Générer un rapport HTML des GPO appliquées
```powershell
gpresult /h C:\rapport-gpo.html
```
WDS - Windows Deployment Services
![Installation de WDS](https://screenshots/15-wds-installation.png)

![Ajout des images de boot](https://screenshots/16-wds-boot-image.png)

![Ajout des images d'installation](https://screenshots/18-wds-install-image.png)

![Test PXE (démarrage réseau](https://screenshots/19-wds-pxe-boot.png)

![Création de l'image de capture (Sysprep)](https://screenshots/23-wds-capture-image.png)


Configuration WDS :

Élément	Détail
Images boot	boot.wim (Windows PE)
Images install	install.wim (Windows 10/11)
Réponse PXE	Tous les clients
Capture	Image sysprepée
MDT - Microsoft Deployment Toolkit

![Installation de MDT](https://screenshots/25-mdt-installation.png)

![DeploymentShare créé](https://screenshots/26-mdt-deployment-share.png)

![Console MMC - Deployment Workbench](https://screenshots/28-mdt-console.png)

![Intégration LiteTouch dans WDS (PXE)](https://screenshots/029-mdt-litetouch-pxe.png)

![Ajout des systèmes d'exploitation](https://screenshots/31-mdt-os-add.png)

![Ajout des applications (Firefox + Drivers VMware)](https://screenshots/05-MDT/25-mdt-applications.png)

![Task Sequence (Windows 10 / Server 2022)](https://screenshots/05-MDT/26-mdt-task-sequence.png)

![Déploiement de la task sequence](https://screenshots/05-MDT/27-mdt-deployment.png)

# Problèmes rencontrés
Erreur BCDBoot
Lors du déploiement final via MDT, une erreur est survenue :

text
Échec de BCDBoot
Source inconnue
Cause : Généralement liée aux partitions/disque
Fichier introuvable
https://screenshots/05-MDT/28-mdt-error-bcdboot.png

# Hypothèses :

- Problème de partitionnement du disque cible (MBR vs GPT)

- Fichiers de boot manquants dans l'image

- Incompatibilité de format de disque

- Tentatives de résolution :

- Vérification des partitions

- Modification du format de disque

- Reconstruction manuelle du BCD

- Le problème n'a pas pu être résolu dans le temps imparti, mais l'infrastructure (AD, DNS, DFS, GPO, WDS, MDT) reste fonctionnelle.


# Ressources
Documentation Active Directory

DFS Replication

Group Policy

Windows Deployment Services

Microsoft Deployment Toolkit

Aides utilisées pour les GPO :

Désactivation firewall : sys-advisor.com

Configuration fond d'écran : techexpert.tips

Désactivation mises à jour : techexpert.tips

# Auteur
Projet réalisé par Syrine dans le cadre du TP Final Architecture Windows.

# Conclusion
Ce projet démontre une maîtrise avancée de l'infrastructure Windows :
Mise en place d'un domaine Active Directory redondé
Configuration DNS intégrée
Déploiement DFS avec réplication
Création et application de 6 GPO
Configuration WDS pour déploiement réseau (PXE)
Installation et configuration MDT
Déploiement final bloqué par erreur BCDBoot
