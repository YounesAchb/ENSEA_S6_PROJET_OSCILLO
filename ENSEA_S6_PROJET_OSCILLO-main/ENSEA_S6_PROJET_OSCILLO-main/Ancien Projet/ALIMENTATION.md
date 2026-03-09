**Systeme d’alimentation** 

**Batterie Renata Li-Po 500mAh :**  source d'énergie. Compacte et fiable, elle offre environ 7 heures d'autonomie.  
**Régulateur LDO AP2112K (3,3V) :** convertit la tension variable de la batterie en un 3,3V stable pour le STM32. Il est choisi pour son faible bruit, indispensable pour ne pas perturber les mesures de l'oscilloscope.  
**Chargeur MCP73831 :** C'est le "cerveau" de la recharge. Il gère le cycle de charge sécurisé de la batterie (CC/CV) pour éviter toute surchauffe.  
**Connecteur USB-C GCT (USB4110) :** port d'entrée pour l'énergie. Il est robuste mécaniquement et possède des pins accessibles pour une soudure manuelle facilitée.  
**Boîtier JST PHR-2 femelle :** C'est la partie en plastique qui se fixe au bout des fils de ta batterie pour permettre un branchement propre sur la carte.  
**Commutateur ON/OFF Würth :** Un interrupteur physique robuste pour allumer ou éteindre ton appareil sans avoir à débrancher la batterie.  
**Transistor BSS84 (MOSFET P) :** Il assure une protection électronique contre l'inversion de polarité. Si la batterie est branchée à l'envers, il coupe instantanément le circuit sans perte de tension.  
**Perle de ferrite Murata :** Elle agit comme un filtre "anti-parasites" pour isoler l'alimentation de la partie mesure (ADC) des bruits générés par le Bluetooth.  
**L'Embase JST PH mâle (B2B-PH-K-S) :** C'est le connecteur que l’on soude sur le PCB. Sans lui, on ne peut pas brancher ton boîtier PHR-2.  
**Les Contacts à sertir JST (SPH-002T-P0.5L) :** Ce sont les petites broches métalliques qu'il faut fixer au bout des fils de la batterie avant de les glisser dans le boîtier PHR-2.

COMPOSANTS QUE JE N’AI PAS RAJOUTÉ AU SOURCING   
**1.Résistances de configuration**

* **2x Résistances de 5,1 kΩ :** À placer sur les pins CC1 et CC2 du port USB-C. Sans elles, les chargeurs modernes (smartphone/PC) ne délivreront aucun courant.  
* **1x Résistance de 2 kΩ :** À brancher sur la puce MCP73831 pour fixer le courant de charge à 500mA (recharge complète en 1h).  
* **2x Résistances de 100 kΩ :** Pour créer un "pont diviseur". Cela permet au STM32 de mesurer la tension de la batterie et d'envoyer le niveau de charge (ex: 75%) à ton smartphone et surtout faire un pont diviseur de tension pour limiter la tension de la batterie .  
* **1x Résistance de 470 Ω :** Pour limiter le courant de la LED de statut.

#### **2\. Condensateurs de stabilité**

* **2x Condensateurs de 10 µF (Céramique) :** Indispensables pour stabiliser l'entrée et la sortie du chargeur MCP73831.  
* **2x Condensateurs de 1 µF (Céramique) :** Nécessaires pour que le régulateur AP2112K fonctionne sans osciller.  
* **Plusieurs condensateurs de 100 nF :** À placer au plus près de chaque puce (STM32, AOP) pour filtrer les micro-parasites.

#### **3\. Indicateur visuel**

* **1x LED (Rouge ou Verte) :** Indispensable pour savoir visuellement si la batterie est en cours de chargement quand tu branches l'USB.

