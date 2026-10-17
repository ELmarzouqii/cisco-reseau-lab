# 🌐 Enterprise Network Topology & Architecture (Cisco Packet Tracer)

Un projet complet d'architecture réseau d'entreprise simulant un environnement hautement disponible, sécurisé et segmenté. Réalisé avec **Cisco Packet Tracer**, ce projet intègre le routage dynamique, la redondance au niveau du passerelle (FHRP), la segmentation VLAN, l'agrégation de liens ainsi que les meilleures pratiques de sécurité et de gestion.

---

## 📌 Aperçu de la Topologie

L'infrastructure est conçue pour garantir la tolérance aux pannes, l'évolutivité et une gestion centralisée des services.

- **Routage dynamique OSPF (Area 0)** inter-routeurs sur liens séries `/30`.
- **Haute disponibilité (HSRP)** pour la redondance des passerelles par défaut.
- **Routage Inter-VLAN (Router-on-a-Stick)**.
- **Agrégation de liens (EtherChannel - PAgP/LACP)** entre commutateurs.
- **Spanning Tree Protocol (STP / PVST+)** optimisé pour éviter les boucles L2.
- **Sécurité des accès (Port-Security)** sur les ports utilisateurs.
- **Services réseau centralisés :** Server DHCP, NTP et Syslog Logging.

---

## 🛠️ Technologies & Protocoles Implémentés

### 1. Routage & Haute Disponibilité
* **OSPFv2 (Open Shortest Path First) :** Zone unique (`Area 0`) connectant tous les routeurs (`Router0`, `Router1`, `Router2`, `Router3`) avec `Router-ID` explicites.
* **HSRP (Hot Standby Router Protocol) :**
  * Groupe `1` sur le sous-réseau `192.168.5.0/24`.
  * **Virtuel IP :** `192.168.5.254`.
  * **Router 3 :** Active (`Priority 120`, `Preempt`).
  * **Router 1 :** Standby (`Priority 110`, `Preempt`).

### 2. Commutation (Switching L2) & Redondance
* **VLAN Segmentation :**
  * `VLAN 10` : DEV (`192.168.1.0/24`)
  * `VLAN 20` : IT (`192.168.2.0/24`)
  * `VLAN 30` : GES (`192.168.3.0/24`)
* **Router-on-a-Stick :** Sous-interfaces configurées sur `Router2` (`Gi0/0.10`, `Gi0/0.20`, `Gi0/0.30`) avec encapsulation `dot1Q`.
* **EtherChannel :** Agrégation de liens entre `Switch0` et `Switch1` (Group 1).
* **PVST+ (Per-VLAN Spanning Tree) :**
  * `Switch5` / `Switch0` configurés en tant que ponts racines (Primary / Secondary Root).
  * `PortFast` & `BPDU Guard` activés sur tous les ports d'accès.

### 3. Sécurité & Administration
* **Port-Security (Switch3) :**
  * Mode `access` sur la plage de ports utilisateurs.
  * Maximum 1 adresse MAC autorisée (`mac-address sticky`).
  * Violation configurée sur `shutdown`.
* **Services Réseau :**
  * **DHCP :** Serveur dédié et pool local DHCP (`Router0`) avec exclusions d'adresses.
  * **DHCP Relay (`ip helper-address`) :** Transit des requêtes DHCP vers le serveur centralisé `192.168.4.2`.
  * **NTP (Network Time Protocol) :** Synchronisation horaire de tous les routeurs sur `192.168.4.2`.
  * **Syslog :** Centralisation des journaux d'événements vers `192.168.4.2`.

---

## 📐 Plan d'Adressage IP (Extrait)

| Équipement | Interface | Adresse IP / Masque | Rôle / Description |
| :--- | :--- | :--- | :--- |
| **Router 3** | `Gi0/0` | `192.168.4.1/24` | Passerelle LAN Admin / Serveurs |
| **Router 3** | `Gi0/1` | `192.168.5.2/24` | HSRP Active Gateway (`VIP: 192.168.5.254`) |
| **Router 1** | `Gi0/0` | `192.168.5.1/24` | HSRP Standby Gateway |
| **Router 0** | `Gi0/0` | `192.168.6.1/24` | Passerelle LAN DHCP |
| **Router 2** | `Gi0/0.10` | `192.168.1.1/24` | Passerelle VLAN 10 (DEV) |
| **Router 2** | `Gi0/0.20` | `192.168.2.1/24` | Passerelle VLAN 20 (IT) |
| **Router 2** | `Gi0/0.30` | `192.168.3.1/24` | Passerelle VLAN 30 (GES) |

---

## 🚀 Comment Tester le Projet

1. Téléchargez et ouvrez le fichier `.pkt` dans **Cisco Packet Tracer** (v8.x ou supérieur recommandé).
2. Attendez la convergence complète des protocoles (STP & OSPF).
3. **Tests de Connectivité (Ping) :**
   - Effectuez un ping depuis un client du VLAN 10 (`192.168.1.X`) vers un client du réseau HSRP (`192.168.5.X`) ou du réseau DHCP (`192.168.6.X`).
4. **Test de Failover HSRP :**
   - Éteignez l'interface `Gi0/1` de `Router3` (`shutdown`).
   - Vérifiez que `Router1` prend le relais instantanément et que le ping vers la VIP `192.168.5.254` ne subit aucune perte majeure.
5. **Test de Sécurité (Port Security) :**
   - Connectez un équipement non autorisé sur une interface de `Switch3` pour vérifier le passage en état `err-disabled` (`shutdown`).

---

## 📝 Auteur
Réalisé par **votre nom / pseudo GitHub** dans le cadre de travaux pratiques en Systèmes et Réseaux.
