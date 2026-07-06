# Analyse de la Qualité de Service (QoS) sur un Réseau IP

Projet de simulation réseau réalisé sous **Cisco Packet Tracer**, visant à concevoir une topologie domestique et à y implémenter une politique de priorisation du trafic (QoS) afin de garantir de bonnes performances aux applications et appareils prioritaires.

## Contexte et problématique

Avec la multiplication des appareils connectés dans les foyers (PC, laptops, téléphones IP, TV smart, etc.), le partage de la bande passante devient un enjeu critique. Ce projet répond à la question :

Comment garantir de bonnes performances pour les applications prioritaires sur un réseau domestique partagé ?

**Solution proposée :** implémentation de mécanismes de QoS (classification, marquage, mise en file d'attente, allocation de bande passante) sur un réseau domestique simulé.

## Objectifs

- Concevoir une topologie réseau domestique intégrant la QoS  
- Implémenter une politique de priorisation du trafic  
- Analyser l'impact de la QoS sur les performances du réseau (débit, latence, pertes de paquets)

## Topologie du réseau

| Équipement | Rôle |
| :---- | :---- |
| HomeRouter (1941) | Routeur principal, passerelle et point d'application de la politique QoS |
| HomeSwitch (2960) | Switch de distribution, marquage CoS |
| Server0 | Serveur (WAN) |
| PC0 – PC3 | Postes clients (zone Bureau principal / zone Famille) |
| IP Phone0 | Téléphone IP |
| Laptop0 | Ordinateur portable |

**Politique QoS définie :** PC0 \= 65 % de la bande passante (trafic prioritaire), Autres appareils \= 35 %.

### Plan d'adressage IP

| Équipement | Adresse IP | Masque | Passerelle |
| :---- | :---- | :---- | :---- |
| PC0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC1 | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 |
| PC2 | 192.168.1.12 | 255.255.255.0 | 192.168.1.1 |
| PC3 | 192.168.1.13 | 255.255.255.0 | 192.168.1.1 |
| Laptop | 192.168.1.14 | 255.255.255.0 | 192.168.1.1 |

## Démarche de configuration de la QoS

1. **Classification du trafic** — Utilisation d'ACL (Access Control Lists) pour identifier les flux à prioriser.  
2. **Création des classes** — Définition des classes `PC0-TRAFFIC` et `OTHER-TRAFFIC` via `class-map`.  
3. **Définition de la politique** — Allocation de bande passante (65 % / 35 %) via `policy-map`.  
4. **Application de la politique** — Attachement de la politique sur l'interface WAN du routeur (`service-policy output`).  
5. **Configuration du marquage** — Attribution de valeurs CoS sur le switch (`mls qos cos`) pour préserver la priorisation au niveau de la couche 2\.

### Extrait de configuration (routeur)

HomeRouter(config)\#class-map match-all PC0-TRAFFIC

HomeRouter(config-cmap)\#match access-group 101

HomeRouter(config)\#class-map match-all OTHER-TRAFFIC

HomeRouter(config-cmap)\#match access-group 103

HomeRouter(config)\#access-list 101 permit ip host 192.168.1.10 any

HomeRouter(config)\#access-list 103 permit ip any any

HomeRouter(config)\#policy-map QOS-POLICY

HomeRouter(config-pmap)\#class PC0-TRAFFIC

HomeRouter(config-pmap-c)\#bandwidth percent 65

HomeRouter(config-pmap-c)\#priority

HomeRouter(config-pmap)\#class OTHER-TRAFFIC

HomeRouter(config-pmap-c)\#bandwidth percent 35

HomeRouter(config)\#interface GigabitEthernet0/1

HomeRouter(config-if)\#service-policy output QOS-POLICY

### Extrait de configuration (switch)

HomeSwitch(config)\#mls qos

HomeSwitch(config)\#interface FastEthernet0/1

HomeSwitch(config-if)\#mls qos cos 5

HomeSwitch(config-if)\#mls qos cos override

HomeSwitch(config)\#interface range FastEthernet0/2-4

HomeSwitch(config-if-range)\#mls qos cos 1

HomeSwitch(config-if-range)\#mls qos cos override

Le routage dynamique **OSPF** est configuré sur le routeur pour l'interconnexion LAN/WAN, avec sécurisation des accès (mots de passe, bannière MOTD, chiffrement des mots de passe).

## Méthodologie de test

- **Scénario 1 — Sans QoS :** tous les appareils génèrent du trafic simultanément, sans priorisation.  
- **Scénario 2 — Avec QoS :** mêmes conditions de trafic, mais avec la politique QoS activée.

**Métriques analysées :**

- Débit effectif par appareil  
- Temps de réponse (latence)  
- Taux de perte de paquets

## Résultats — PC0 (trafic prioritaire)

- Augmentation significative du débit effectif (conforme aux 65 % de bande passante alloués)  
- Réduction importante de la latence  
- Diminution des pertes de paquets  
- Impact direct positif sur les applications prioritaires (travail à distance, visioconférence)

## Avantages d'une QoS domestique

| Cas d'usage | Bénéfice |
| :---- | :---- |
| Télétravail | Stabilité des visioconférences et applications professionnelles |
| Divertissement | Streaming vidéo fluide même en période de forte utilisation |
| Cohabitation | Partage équitable des ressources entre utilisateurs |
| Expérience globale | Réduction des conflits d'utilisation et des frustrations |

## Défis et limitations

- Nécessite des équipements réseau supportant les fonctionnalités QoS avancées  
- Configuration plus complexe qu'un réseau domestique standard  
- La QoS ne peut pas créer de bande passante supplémentaire, elle ne fait que la répartir  
- Nécessité d'adapter régulièrement la politique selon l'évolution des usages

## Perspectives d'amélioration

- QoS basée sur les applications plutôt que sur les appareils  
- Intégration des dispositifs IoT dans la politique QoS  
- Mise en place d'une politique dynamique s'adaptant aux besoins en temps réel  
- Combinaison avec des techniques de mise en cache pour optimiser davantage le réseau

## Conclusion

La QoS est un outil puissant pour optimiser les performances d'un réseau domestique. L'implémentation réalisée démontre des améliorations significatives pour les appareils prioritaires, et l'allocation de bande passante (65 %/35 %) s'est révélée efficace pour le cas d'usage étudié. La QoS devient un mécanisme de plus en plus essentiel avec la multiplication des appareils connectés et des usages gourmands en bande passante.

## Outils utilisés

- **Cisco Packet Tracer** (v8.2.2) — simulation de la topologie réseau, configuration CLI (routeur/switch) et test des scénarios QoS

## Structure suggérée du dépôt

.

├── README.md

├── topologie/          \# Fichier .pkt Packet Tracer

├── configs/            \# Extraits de configuration (routeur, switch)

└── docs/               \# Rapport / présentation du projet  
