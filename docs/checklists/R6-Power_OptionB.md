# R6-POWER Option B — Checklist source unique
CHECKLIST DÉFINITIVE — HUB R6-POWER OPTION B SANS MCU
V1.0

BLOC A — ENTRÉE PUISSANCE + PROTECTIONS  
          
Façade GAUCHE.

+12V BAT EXT borne M8 rouge traversante → câble 2 AWG → porte-fusible ANL 150 A ≤ 20 cm batterie → ENTRÉE +12V.

GND CHÂSSIS EXT borne M8 noire traversante → point étoile M8 interne → tôle poncée, rondelle éventail.

ENTRÉE +12V → relais principal 12 V 60 A en série → LM5050-1 + 2 MOSFET N back-to-back → BUS +12V.

ENTRÉE +12V → TVS SM8S36A bidirectionnelle vers châssis.

BUS +12V → 2× 4700 µF 25 V et 1× 100 nF X7R → masse.

Rappels polarité

TVS SM8S36A bi: pas de polarité, une patte au ENTRÉE +12V, l’autre au châssis.

Électrolytiques: + au bus, − à la masse.

BLOC B — PRÉCHARGE + BY-PASS RELAIS

Précharge anti-étincelle: ENTRÉE +12V → R 10 Ω 5 W → BUS +12V.

Bobine relais: BUS +12V → diode 1N5819 → bobine → NPN → masse.

Commande bobine = PG_DELAY du BLOC F.

Diode 1N5819: bande = cathode côté BUS +12V.

BLOC C — ALIM LOGIQUE 5 V

BUS +12V → self 10 µH → 47 µF 25 V + 100 nF → masse → buck 12→5 V 1 A → +5V LOGIQUE.

+5V LOGIQUE → TVS SMBJ5.0A, 47 µF 10 V, 100 nF → masse.

TLV803S 4,63 V: Vdd=+5 V, GND=masse, RESET bas = FAULT_P5.

Adaptateurs veroboard

TLV803S SOT-23 → adaptateur SOT-23 → DIP3.

BLOC D — ENTRÉES UTILISATEUR + DÉTECTIONS

ACC isolé

Façade GAUCHE: ACC_IN EXT bornier 5,08 mm.

ACC_IN → 10 kΩ → LED H11L1 → masse.

Collecteur H11L1 → 10 kΩ → +5 V. Émetteur → masse.

Sortie collecteur = ACC_OK (haut = OK).

UVLO / OVLO

LM393 + TL431 2,5 V. Diviseurs depuis BUS +12V.

Seuils: UVLO desc 11,2 V, reprise 11,8 V. OVLO mont 16,2 V, reprise 15,8 V.

Sorties open-collector: FAULT_UV, FAULT_OV.

Thermique

NTC 10 kΩ sur radiateur MOSFET, pont 10 k/NTC → LM393.

Arrêt 95 °C, reprise 85 °C → FAULT_TH.

OR des fautes

FAULT_UV, FAULT_OV, FAULT_TH, FAULT_P5 via 1N4148 → nœud FAULT_L.

FAULT_L → 10 kΩ → +5 V.
Diodes 1N4148: bande = cathode côté FAULT_L.

LM393 DIP8, TL431 TO-92, H11L1 DIP6, OK veroboard.

BLOC E — INTERRUPTEUR ON/OFF UTILISATEUR

Façade HAUT: interrupteur panel SPST 12 V.

+5V LOGIQUE → COM de l’interrupteur.

Sortie ON → SW_ON.

Pull-down 100 kΩ de SW_ON vers masse. OFF coupe tout.

BLOC F — LOGIQUE ENABLE + DÉLAI RELAIS (CD4093B, DIP14)

CD4093B alimenté en +5 V.

Inverser FAULT_L avec 1 porte: entrées liées sur FAULT_L → sortie = OK_FAULT (haut = pas de défaut).

ENABLE = AND(ACC_OK, OK_FAULT, SW_ON) avec NAND + inverseur.

PG_DELAY ≈ 100 ms: ENABLE → R 150 kΩ → nœud X ; X → C 1 µF → masse ; X sur une entrée, ENABLE sur l’autre.

1N4148 en // de R 150 kΩ, cathode côté ENABLE.

CD4093 DIP14, OK veroboard.

BLOC G — SÉQUENCEMENT REMOTE ×6 SANS MCU

Objectif ON: +50 ms entre canaux. OFF: CH3..CH6 immédiat, CH1..CH2 +150 ms.

G1. Retard ON_i par canal i = 1..6

ENABLE → R_i → nœud D_i.

D_i → C_i → masse.

D_i → entrée A de la porte CD4093 du canal i. ENABLE → entrée B.

Sortie de la porte = ON_i.

1N4148 en // de R_i: cathode côté ENABLE, anode côté D_i.

Valeurs avec C_i = 1 µF X7R:
R1=0 Ω, R2=75 kΩ, R3=150 kΩ, R4=220 kΩ, R5=300 kΩ, R6=360 kΩ.

G2. OFF rapide et OFF 150 ms

OFF_FAST = NON(ENABLE) avec 1 porte en inverseur.

OFF_SLOW150: OFF_FAST → R 220 kΩ → nœud Y ; Y → 1 µF → masse ; Y sur entrée A, OFF_FAST sur entrée B ; sortie = OFF_SLOW150.

1N4148 en // de R 220 kΩ: cathode côté OFF_FAST.

G3. Autorisation finale REM_EN_i

CH1..CH2: REM_EN_i = ON_i ET NON(OFF_SLOW150) via NAND puis inverseur.

CH3..CH6: REM_EN_i = ON_i ET NON(OFF_FAST) via NAND puis inverseur.

G4. Étages de puissance par canal

P-MOSFET FQP27P06:

Source → BUS +12V.

Drain → PTC 0,25 A → borne REMOTE_OUT_i.

Gate → 100 kΩ → Source.

Gate ↔ 47 nF ↔ Source.

Zener 1N4744A 15 V entre Gate et Source: cathode à la Source, anode à la Gate.

Driver NPN 2N2222A:

Collecteur → Gate P-MOSFET.

Émetteur → masse.

Base ← 1 kΩ ← REM_EN_i.

1N4148 en // de la 1 kΩ: anode à la base, cathode au REM_EN_i.

TVS SMCJ18A unidirectionnelle: entre REMOTE_OUT_i et masse. Cathode au REMOTE_OUT_i, anode à la masse.

LED canal: REMOTE_OUT_i → LED → 2,2 kΩ → masse. LED anode côté REMOTE_OUT_i.

BLOC H — ANTI-BOUCLE 0V_AUDIO

Façade DROITE: 0V_AUDIO EXT borne M5.

Pont redresseur 25 A entre 0V_AUDIO et châssis: + et − ensemble au 0V_AUDIO. ~ et ~ ensemble au châssis.

En parallèle: 10 Ω 5 W + Y2 4,7 nF entre 0V_AUDIO et châssis.

BLOC I — DISTRIBUTION COURANT

Façade DROITE.

OUT_A1..A4: BUS +12V → 4× porte-fusibles MIDI encastrés → bornes M6.

OUT_D1..D2: BUS +12V → 2× mini-fusibles 3–7,5 A → bornes M4.

Couples: M6 6–7 N·m, M8 14–16 N·m.

BLOC J — COMPTEUR DC430-100A 8-EN-1 AVEC SHUNT

Shunt 100 A 75 mV obligatoire. Câblage low-side.

J1. Trajet masse
Carrosserie → câble masse → GND CHÂSSIS EXT → SHUNT → point étoile M8 interne → masses du hub.

J2. Fils ÉPAIS du module vers shunt

Fil épais ROUGE du module → vis shunt côté hub.

Fil épais NOIR du module → vis shunt côté carrosserie.

J3. Fils FINs du module (alimentation + mesure tension)

Fin ROUGE “working power +” → BUS +12V après relais et LM5050.

Fin NOIR “working power −” → masse après shunt au point étoile interne.

Fin JAUNE “voltage test +” → BUS +12V après relais et LM5050.
Plage d’alim typique du module: 4–30 V DC .

J4. Sonde T° du module
Fixer sur radiateur MOSFET. Brancher au connecteur NTC du module.

J5. Vérifs

À vide: V ~12–14,5 V, A ≈ 0,00.

Courant négatif affiché: inverser les deux fils épais sur le shunt.

Ne jamais relier les deux fils épais du module directement au + et − de l’alim .

BLOC K — CONNECTEURS EXTÉRIEURS

Entrée gauche

+12V BAT EXT M8 rouge

GND CHÂSSIS EXT M8 noire

ACC_IN EXT bornier 5,08 mm

ON/OFF interrupteur SPST 12 V

Compteur DC430 intégré en façade

Sortie droite

OUT_A1..A4 M6 après MIDI

OUT_D1..D2 M4 après mini

REMOTE_OUT_1..6 borniers 5,08 mm

0V_AUDIO EXT M5

BLOC L — CÂBLAGE UTILISATEUR

Poser ANL 150 A côté batterie.

Câbler +12V BAT EXT et GND CHÂSSIS EXT.

Prendre ACC via add-a-fuse vers ACC_IN EXT.

Inter sur OFF pour brancher, puis ON pour activer.

Relier amplis et DSP aux sorties.

REMOTE des appareils sur REMOTE_OUT_i.

Vérifier le compteur.

BLOC M — TESTS

OFF: brancher. Rien ne s’allume.

ON: LED 5 V OK <150 ms, relais colle ~100 ms, pas d’étincelle.

ACC: REMOTE 1→6 montent par pas de 50 ms.

OFF: CH3..6 coupent instant, CH1..2 après 150 ms, relais ouvre.

UVLO 11,0 V: OFF global, LED UVLO ON.

OVLO 16,4 V: OFF global, LED OVLO ON.

Thermique 95 °C: OFF global, LED THERM ON, reprise 85 °C.

Start-stop: creux 6,5 V 300 ms, extinction propre.

BLOC N — LM5050-1 + 2 MOSFET N BACK-TO-BACK SUR VEROBOARD

Adaptateurs

LM5050-1 SOIC-8 → adaptateur SOIC-8 → DIP-8.

MOSFET N TO-220 Rds_on très faible, sur radiateur.

Nœuds

A = sortie du relais principal

B = sources communes des 2 MOSFET N

C = BUS +12V

GATE_N = gates communes des 2 MOSFET N

Câblage MOSFET

MOSFET #1: Drain → A, Source → B, Gate → GATE_N

MOSFET #2: Drain → C, Source → B, Gate → GATE_N

Zener 15 V 1N4744A entre Gate et Source: cathode sur B, anode sur GATE_N

100 kΩ entre Gate et Source

47 nF entre Gate et Source

LM5050-1 vers nœuds

GND → masse

SOURCE/SENSE → B

IN/VIN → A

OUT/VOUT → C

GATE → série 10 Ω → GATE_N

EN si présent → IN via 100 kΩ (ON par défaut)

Vérif visuelle: les diodes internes des deux MOSFET doivent être opposées. Si tu vois qu’elles “pointent” dans le même sens, tu as inversé Drain/Source d’un des MOSFET.
