# AD-Attack-Defense-Lab
Active Directory Attack &amp; Defense Lab - Kill Chain Simulation &amp; Wazuh Custom Rules
# 🛡️ AD Attack & Defense Lab — Threat-Informed Detection Engineering

![Wazuh](https://img.shields.io/badge/SIEM-Wazuh_4.x-blue)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE_ATT%26CK-4_Techniques-green)
![Lab Status](https://img.shields.io/badge/Status-Completed-success)

Simulation d'une Kill Chain Active Directory complète (Red Team) et ingénierie de règles de détection SIEM personnalisées (Blue Team), inspirées du rapport d'incident **DFIR Report — BlackSuit Ransomware (août 2024)**.

---

## 🎯 Vue d'ensemble de la Kill Chain (Red Team)

| Étape | Technique | MITRE ATT&CK | Outil utilisé | Résultat |
|:-----:|-----------|:------------:|---------------|----------|
| **1** | Reconnaissance | `T1087.002` | BloodHound CE | Cartographie complète du domaine |
| **2** | Kerberoasting | `T1558.003` | GetUserSPNs + Hashcat | `svc:password123!` |
| **3** | LSA Secrets | `T1003.004` | impacket-secretsdump | `emp1:emplo123!` (Mot de passe en clair) |
| **4** | DCSync | `T1003.006` | secretsdump `-just-dc-ntlm` | Hash `krbtgt` — Compromission totale |

---

## 🚀 Règles de Détection Custom (Wazuh SIEM)

* **Temps Moyen de Détection (MTTD) :** `~15 secondes`
* **Couverture de la Kill Chain :** `4/4 techniques (100%)`

| ID Règle | Technique Cible | Niveau | Event ID | Signal / IOC Déterminant |
|:--------:|-----------------|:------:|:--------:|--------------------------|
| **100001** | Kerberoasting | **12/15** | `4769` | Ticket TGS demandé en chiffrement RC4 (`0x17`) |
| **100002** | LSA Secrets | **10/15** | `7045` | Création de service sous un compte domaine (`LAB\`) |
| **100003** | Mouvement Latéral NTLM | **10/15** | `4624` | Logon Type 3 NTLM depuis réseau interne |
| **100004** | DCSync | **15/15** | `4662` | Présence du GUID `1131f6ad...` (GetChangesAll) |

---

## 📐 Architecture du Laboratoire

```text
+-----------------------------------------------------------------------------------+
|                        VMware Fusion — Réseau Isolé (VLAN)                        |
|                                                                                   |
|  [Kali Linux 2024]  ============ Attaques =============>  [DC-2022 / Windows 11]  |
|   192.168.10.100                                            192.168.10.10 / .20   |
|                                                                   ||              |
|                                                            Agent  || TCP 1514     |
|                                                            Wazuh  \/              |
|  [macOS Host]       <====== HTTPS / 443 =================  [Wazuh SIEM Manager]   |
|   Navigateur Web                                            192.168.10.30         |
+-----------------------------------------------------------------------------------+
## 📄 Rapport Complet

Le rapport technique complet (30 pages) est disponible dans `/report/`.
Il inclut :
- Kill chain complète avec captures d'écran
- IOCs analysés dans Wazuh Discover
- Règles Wazuh XML commentées
- Métriques SOC (MTTD, faux positifs)
- Remédiations priorisées
