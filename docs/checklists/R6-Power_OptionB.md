R6-POWER Option B — Checklist source unique

Version courante: v1.3

Objectif

Hub d’alimentation car audio sans MCU. Remote ×6 anti-pop. Protections UV, OV, thermique. Diode idéale LM5050-1 + 2 MOSFET N back-to-back. Compteur DC430-100A. Montage veroboard traversant.

Sommaire

Connecteurs façade

Entrée puissance et protections

Précharge et relais

Alimentation 5 V logique

Détecteurs ACC, UV, OV, Thermique

Interrupteur ON OFF

Enable et délai relais

Séquencement Remote ×6

Anti-boucle masse audio

Distribution sorties

Compteur DC430-100A

Tests sécurité

Bloc LM5050-1 et MOSFET back-to-back

Fusible ANL recommandé

Polarités et rappels rapides

Schéma mental rapide
BAT+ → ANL → Relais → LM5050-1 + 2×MOSFET N → BUS +12V → Sorties
BAT− → Châssis → Shunt 100 A → Point étoile masse → Sorties
ACC → Opto → Logique 5 V → Enable + Séquencement Remote
DC430: fils épais sur shunt, fins sur BUS +12V et masse après shunt

1. Connecteurs façade

Entrée gauche:

+12V BAT EXT sur borne M8 rouge

GND CHÂSSIS EXT sur borne M8 noire

ACC_IN EXT sur bornier 5,08 mm

Inter ON OFF SPST 12 V

Fenêtre du compteur DC430

Sortie droite:

OUT_A1..A4 sur bornes M6 après fusibles MIDI

OUT_D1..D2 sur bornes M4 après mini-fusibles

REMOTE_OUT_1..6 sur borniers 5,08 mm

0V_AUDIO EXT sur borne M5

2. Entrée puissance et protections

+12V BAT EXT → ANL = 1,25 × I_cont → ENTREE +12V → Relais 12 V 60 A → LM5050-1 + 2 MOSFET N back-to-back → BUS +12V

TVS entrée: SM8S36A bidirectionnelle entre ENTREE +12V et châssis

Réservoir bus: BUS +12V → 2× 4700 µF 25 V + 100 nF → masse

Précharge: résistance 10 Ω 5 W flameproof entre ENTREE +12V et BUS +12V

3. Précharge et relais

La 10 Ω reste en place en permanence

Bobine relais: BUS +12V → 1N5819 → bobine → NPN → masse

Commande NPN = signal PG_DELAY

4. Alimentation 5 V logique

BUS +12V → self 10 µH → 47 µF + 100 nF → buck 12 → 5 V 1 A → +5V LOGIQUE

Protection 5 V: TVS SMBJ5.0A + 47 µF + 100 nF vers masse

Superviseur: TLV803S 4,63 V. Sortie RESET bas = FAULT_P5

5. Détecteurs ACC, UV, OV, Thermique

ACC isolé: ACC_IN → 10 kΩ → LED H11L1 → masse. Collecteur → 10 kΩ → +5 V. Sortie = ACC_OK (haut = OK)

UV OV: LM393 + TL431

UVLO 11,2 V descente, reprise 11,8 V → FAULT_UV

OVLO 16,2 V montée, reprise 15,8 V → FAULT_OV

Thermique: NTC 10 kΩ au radiateur MOSFET. Arrêt 95 °C, reprise 85 °C → FAULT_TH

OR des fautes: FAULT_UV, FAULT_OV, FAULT_TH, FAULT_P5 via 1N4148 → ligne FAULT_L tirée au +5 V par 10 kΩ

6. Interrupteur ON OFF

+5V LOGIQUE → SPST → SW_ON

Pull-down SW_ON: 100 kΩ vers masse

OFF coupe toute logique

7. Enable et délai relais

OK_FAULT = NON(FAULT_L)

ENABLE = AND(ACC_OK, OK_FAULT, SW_ON) réalisé avec CD4093B (NAND + inverseur)

Délai relais PG_DELAY ≈ 100 ms:

ENABLE → 150 kΩ → nœud X

X → 1 µF → masse

Porte NAND avec entrées = ENABLE et X

1N4148 en parallèle de 150 kΩ, cathode côté ENABLE

Coupe thermique passive de la bobine:

Thermostat NC 90 à 95 °C en série dans la ligne de commande de bobine

8. Séquencement Remote ×6

Objectif ON: +50 ms par canal. Objectif OFF: CH3..CH6 immédiat, CH1..CH2 +150 ms.

Retards ON par canal i = 1..6:

ENABLE → R_i → D_i

D_i → 1 µF → masse

D_i → entrée A de la porte

ENABLE → entrée B de la porte

Sortie porte = ON_i

1N4148 en parallèle de R_i, cathode côté ENABLE

Valeurs RC typiques avec C_i = 1 µF X7R:

Canal	R_i	C_i
CH1	0 Ω	1 µF
CH2	75 kΩ	1 µF
CH3	150 kΩ	1 µF
CH4	220 kΩ	1 µF
CH5	300 kΩ	1 µF
CH6	360 kΩ	1 µF

OFF rapide et OFF +150 ms:

OFF_FAST = NON(ENABLE)

OFF_SLOW150: OFF_FAST → 220 kΩ → Y ; Y → 1 µF → masse ; porte avec entrées OFF_FAST et Y → sortie OFF_SLOW150 ; 1N4148 en parallèle de 220 kΩ, cathode côté OFF_FAST

Autorisation finale REM_EN_i:

CH1..CH2: REM_EN_i = ON_i ET NON(OFF_SLOW150)

CH3..CH6: REM_EN_i = ON_i ET NON(OFF_FAST)

Étages puissance Remote, par canal:

P MOSFET FQP27P06

Source → BUS +12V

Drain → PTC 0,25 A → REMOTE_OUT_i

Gate → 100 kΩ → Source

Gate ↔ 47 nF ↔ Source

Zener 15 V 1N4744A: cathode Source, anode Gate

NPN 2N2222A

Collecteur → Gate P MOSFET

Émetteur → masse

Base ← 1 kΩ ← REM_EN_i

1N4148 en parallèle du 1 kΩ, anode base, cathode REM_EN_i

TVS canal: SMCJ18A unidirectionnelle, cathode au REMOTE_OUT_i, anode masse

LED canal: REMOTE_OUT_i → LED → 2,2 kΩ → masse

9. Anti-boucle masse audio

0V_AUDIO EXT sur borne M5

Pont 25 A: + et − réunis côté 0V_AUDIO, les deux symboles ~ réunis côté châssis

En parallèle: 10 Ω 5 W et condensateur Y2 4,7 nF

10. Distribution sorties

OUT_A1..A4: BUS +12V → fusibles MIDI → bornes M6

OUT_D1..D2: BUS +12V → mini-fusibles 3 à 7,5 A → bornes M4

Couvre-bornes isolants

Couples: M6 6 à 7 N·m, M8 14 à 16 N·m

Re-vérification du serrage à 24 h

11. Compteur DC430-100A

Chemin masse low-side: Carrosserie → GND CHÂSSIS EXT → Shunt 100 A 75 mV → Point étoile M8 → masses du hub

Raccordements module:

Fils épais

ROUGE sur la vis du shunt côté hub

NOIR sur la vis du shunt côté carrosserie

Faisceau 3 fils fin

ROUGE “power +” sur BUS +12V

NOIR “power −” sur masse après shunt au point étoile

JAUNE “voltage +” sur BUS +12V

Important: ne jamais mettre les deux fils épais directement sur + et − de l’alimentation.

12. Tests sécurité

Court-circuit bus: l’ANL doit fondre sans flamme

Inversion polarité: LM5050-1 bloque le retour

Surchauffe boîtier: thermostat ouvre le relais vers 95 °C

Surintensité remote: PTC de canal déclenche

Vibrations 20 à 30 Hz pendant 15 min

Start-stop: creux 6,0 V pendant 500 ms

Brumisation eau

Critères d’acceptation: aucune fumée, pas de dépôt carbone, MOSFET < 100 °C

13. Bloc LM5050-1 et MOSFET back-to-back

Nœuds:

A = sortie relais

B = sources communes des MOSFET

C = BUS +12V

GATE_N = gates communes

Câblage MOSFET N:

MOSFET 1: Drain → A, Source → B, Gate → GATE_N

MOSFET 2: Drain → C, Source → B, Gate → GATE_N

Diodes internes opposées

Clamps de gate:

Zener 15 V: cathode sur B, anode sur Gate

100 kΩ Gate vers B

47 nF Gate vers B

LM5050-1:

GND → masse

SOURCE/SENSE → B

IN → A

OUT → C

GATE → 10 Ω → GATE_N

EN → IN via 100 kΩ

Boîtier SOIC-8 sur adaptateur SOIC-8 vers DIP-8 pour veroboard

14. Fusible ANL recommandé
I_continu	ANL recommandé
40 à 60 A	70 à 80 A
60 à 80 A	80 à 100 A
80 à 100 A	100 à 125 A
100 à 120 A	125 à 150 A
15. Polarités et rappels rapides

Bande d’une diode = cathode

LED: anode au plus

Électrolytiques: borne + au BUS, borne − à la masse

TVS SM8S36A entrée: bidirectionnelle, pas de polarité

Temps de retard approximatif: T ≈ 0,693 × R × C

Toujours mettre l’interrupteur sur OFF avant de brancher

Pas de mousse non UL94 V-0 dans le boîtier

En cas d’odeur chaude: OFF, contrôle fusibles et couples
