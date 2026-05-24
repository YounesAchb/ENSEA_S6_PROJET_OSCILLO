# ENSEA S6 — Projet Oscilloscope Portable Connecté

Ce dépôt contient l'intégralité des sources (conception matérielle KiCad, firmware STM32 et application Java Android) du projet de fin de S6 réalisé à l'ENSEA. 

## 👥 Étudiants concernés
* **Yassine JABOU**
* **Younes ACHBAD**
* **Gabriel BOUDOUX D'HAUTEFEUILLE**
* **Gökay OZDEN**

---

## 📝 Abstract & Contexte de Fin de Projet
Le but de ce projet est de concevoir un système embarqué autonome agissant comme un oscilloscope de précision déporté. L'écosystème est divisé en deux parties principales :
1. **Un boîtier d'acquisition autonome :** Reçoit le signal analogique, adapte son amplitude, le numérise via un STM32L476RG et transmet les trames via un module Bluetooth HC-05.
2. **Une interface utilisateur (Display) :** Une application Android native développée en Java permettant la visualisation en temps réel, le contrôle des calibres, les mesures par curseurs et le traitement fréquentiel (FFT).

⚠️ **Statut Réel du Projet (Post-Soutenance) :** Suite à un retard de livraison critique de notre fournisseur sur le composant analogique central (l'amplificateur à gain programmable PGA849), le PCB final n'a pas pu être assemblé et soudé à temps pour les tests physiques. 
Cependant, l'ensemble du projet a été validé avec succès par une **approche de virtualisation et de simulation** :
* **Hardware :** PCB double couche entièrement routé et validé par DRC sous KiCad.
* **Firmware C :** 100% fonctionnel, validé sur carte Nucleo en simulant l'absence du PGA.
* **Application Android :** 100% fonctionnelle, dotée d'un banc de simulation générant des signaux virtuels cohérents permettant de valider l'interface utilisateur, le protocole de communication et les algorithmes de traitement (FFT, curseurs).

---

## 🛠 Spécifications du Projet

### 1. Performances & Objectifs Communs
* **Nombre d'entrées :** 2 voies (1 voie implémentée et validée sur cette version).
* **Bande passante visée :** 10 Hz - 50 kHz.
* **Résolution d'acquisition :** 12 bits.
* **Échantillonnage :** 10 kHz fixe, rythmé de manière matérielle par Timer.
* **Points par capture :** Buffer circulaire de 1024 points (gestion par demi-tableaux de 512 points).
* **Communication :** Liaison Bluetooth SPP (Standard Serial Port Profile) à 115200 bauds.
* **Gestion de l'énergie :** Objectif d'autonomie de 6 heures sur piles (sélection de composants Low-Power).

### 2. Architecture Matérielle (Hardware)

Le routage a été optimisé sur un PCB compact double couche. La conception repose sur une séparation stricte des masses et une cascade d'alimentation spécifique pour préserver la propreté du signal de référence ($V_{ref}$) de l'ADC.

#### Schéma Bloc de l'Architecture Électronique
```mermaid
graph TD
    classDef power fill:#2d3436,stroke:#deff9a,stroke-width:2px,color:#fff;
    classDef analog fill:#d63031,stroke:#fff,stroke-width:2px,color:#fff;
    classDef digital fill:#0984e3,stroke:#fff,stroke-width:2px,color:#fff;
    classDef com fill:#00b894,stroke:#fff,stroke-width:2px,color:#fff;

    subgraph Alimentation & Énergie
        Piles((Piles 3xAA)) -->|BAT_PLUS| Switch[Interrupteur + Diode Schottky]
        Switch -->|Vin| LDO3[LDO 3.3V: AP2112K]:::power
        Switch -->|Vin| LDO5[LDO 5V: MCP1826S]:::power
        LDO5 -->|Sortie 5V| Traco[DC/DC Symétrique: Traco TSM]:::power
        Traco -->|+15V / -15V| PGA
    end

    subgraph Chaîne Analogique
        BNC((BNC Verticale 50Ω)) -->|Signal Brut| PGA[Ampli de précision: PGA849]:::analog
        PGA -->|Signal Conditionné| Clamping[Diode de protection: BAT54S]:::analog
        Clamping -->|Entrée Analogique PA0| MCU
    end

    subgraph Traitement & Communication
        LDO3 -->|V3.3_DIG| MCU[MCU: STM32L476RG]:::digital
        MCU -->|Sélection Calibre 3-bits| PGA
        LDO5 -->|5V BT| BT[Module Bluetooth: HC-05]:::com
        MCU <-->|UART 115200 bauds| BT
        BT -.->|Sans-fil SPP| Android((App Android Java)):::com
    end
