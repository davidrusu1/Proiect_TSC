# InkTime — Smartwatch Open Source

Un smartwatch proiectat de la zero — schematic, PCB 4 straturi, model 3D complet — bazat pe nRF52840 și un display E-Paper cu consum zero în standby.

---

## Despre proiect

InkTime este un ceas inteligent construit integral custom: fiecare componentă a fost aleasă, plasată și rutată manual, de la primul simbol în schematic până la modelul 3D pregătit pentru fabricație.

Tehnologia centrală este display-ul **E-Paper (e-ink)** — după ce afișează o imagine, o menține fără niciun consum de energie. Combinat cu modul deep sleep al nRF52840 (~1.5μA), dispozitivul poate funcționa zile întregi dintr-o baterie LiPo mică.

---

## Ce conține placa

| Subsistem | Componentă | Rol |
|---|---|---|
| Microcontroller | nRF52840 | Cortex-M4 64MHz, BT 5.0, USB nativ |
| Display | E-Paper via SPI | Afișaj fără consum în standby |
| Mișcare | BMA423 | Accelerometru 3 axe, step counter, wake-on-wrist |
| Încărcare | BQ25180 | LiPo charger programabil I2C, 5mA–1A |
| Alimentare | RT6160 | Buck-Boost 3.3V stabil indiferent de tensiunea bateriei |
| Fuel Gauge | MAX17048 | Monitorizare baterie ModelGauge, fără shunt rezistor |
| Feedback haptic | DRV2605 | Driver haptic I2C, 123 efecte ERM/LRA |
| USB | KH-TYPE-C-16P + USBLC6 | Conector USB-C cu protecție ESD ±15kV |
| Butoane | EVP-AKE31A ×3 | Butoane tactile SMD ultra-subțiri |
| RF | 2450AT18B100E | Antenă SMD 2.45GHz pentru Bluetooth |

---

## Diagrama bloc a sistemului

```
USB-C (5V)
    │
    ▼
┌─────────┐    VBUS      ┌──────────────┐
│  ESD    │─────────────▶│  BQ25180     │ LiPo Charger
│USBLC6   │              │  (I2C 0x6A)  │◀─── Baterie LiPo
└────┬────┘              └──────┬───────┘
     │ D+/D−                    │ VBAT
     │                          ▼
     │                   ┌──────────────┐
     │                   │  MAX17048    │ Fuel Gauge
     │                   │  (I2C 0x36)  │
     │                   └──────┬───────┘
     │                          │ VBAT
     │                          ▼
     │                   ┌──────────────┐
     │                   │   RT6160     │ Buck-Boost DC/DC
     │                   │  (I2C 0x62)  │─── 3V3 ──────────┐
     │                   └──────────────┘                   │
     │                                                      │
     └──────────────────────────────────────────────────────▼
                                                    ┌──────────────────┐
                  ┌── SPI ──▶ E-Paper Display       │                  │
                  │                                  │    nRF52840      │
                  ├── I2C ──▶ BMA423  (0x18)        │    Cortex-M4     │
                  │       ──▶ DRV2605 (0x5A)        │    64MHz / BT5.0 │
                  │       ──▶ BQ25180 (0x6A)        │                  │
                  │       ──▶ RT6160  (0x62)        └────────┬─────────┘
                  │       ──▶ MAX17048(0x36)                 │
                  │                                          │ GPIO
                  ├── SWD ──▶ TC2030-IDC                     │
                  │                                          ▼
                  └── USB ──▶ (intern nRF52840)      Butoane ×3 / IMU INT / HAPTIC_EN
```

---

## BOM — Bill of Materials

| Ref | Part Number | Descriere | Qty |
|---|---|---|---|
| U1 | nRF52840-QIAA-R | MCU ARM Cortex-M4, BT 5.0, USB 2.0, 1MB Flash | 1 |
| IC1 | BQ25180YBGR | LiPo charger liniar 1A I2C, 8-DSBGA | 1 |
| IC2 | DRV2605YZFR | Haptic driver ERM/LRA I2C 123 efecte, 9-BGA | 1 |
| IC3 | BMA423 | Accelerometru MEMS triaxial 12-bit, LGA-12 | 1 |
| U3 | MAX17048G+T10 | Fuel gauge ModelGauge 1-cell, SOT-23-6 | 1 |
| IC9 | RT6160AWSC | Buck-Boost DC/DC 800mA I2C, 15-WL-CSP | 1 |
| D3 | USBLC6-2SC6Y | TVS ESD ±15kV bidirecțional USB, SOT-23-6 | 1 |
| J4 | KH-TYPE-C-16P | Conector USB-C 16 pini SMD | 1 |
| J1 | 503480-2400 | Conector FPC/FFC 0.5mm pas 24 circuite | 1 |
| J2 | TC2030-IDC | Tag-Connect SWD 6 pini footprint-only | 1 |
| Q2 | DMG2305UX-7 | P-MOSFET 20V/4.2A, SOT-23 | 1 |
| Q3 | SI1308EDL-T1-GE3 | N-MOSFET 30V/1.5A, SC-70 | 1 |
| D1,D2,D5 | MBR0530 | Schottky 30V/500mA, SOD-123 | 3 |
| SW_* | EVP-AKE31A | Buton tactil SMD ultra-subțire Panasonic | 3 |
| ANT1 | 2450AT18B100E | Antenă SMD 2.45GHz Johanson | 1 |
| L7 | FTC252012SR47MBCA | Inductor 0.47μH SMD 2016 | 1 |
| L5 | 744043680 | Inductor 68μH WE-TPC SMD | 1 |
| X1 | SXT21410EC27-32.000M | Cristal SMD 32MHz | 1 |
| X2 | NX3215SA-32.768K | Cristal RTC 32.768kHz 3215 SMD | 1 |
| Cx | GJM0335C1ER33WB01D / GRM011R60J152KE01L | Condensatoare SMD 0201/0402, 1pF–22μF | ~50 |
| Rx | ERA-1AEB / CPF0201 | Rezistoare SMD 0201, 2.2Ω–10kΩ | ~15 |
| TP_* | — | Test pad SMD | 14 |

---

## Hardware în detaliu

### Cum circulă energia

Bateria LiPo (3.7–4.2V) furnizează tensiunea de intrare pentru regulatorul **RT6160**, care generează un rail stabil de 3.3V pentru întreaga placă. La conectarea USB-C, VBUS (5V) ajunge direct la **BQ25180**, care preia controlul încărcării bateriei independent de restul sistemului. nRF52840 monitorizează în permanență cele două stări: prezența USB prin semnalul CHARGER_PG și nivelul bateriei prin **MAX17048** pe magistrala I2C.

Componentele cu consum ridicat — display-ul E-Paper și driverul haptic — sunt controlate fiecare printr-un MOSFET (Q2/Q3), permițând nRF52840 să le întrerupă complet alimentarea când nu sunt necesare.

---

### Magistrala I2C — 5 periferice pe aceleași fire

| Componentă | Adresă I2C | Funcție |
|---|---|---|
| BQ25180 | 0x6A | Reglează curentul și tensiunea de încărcare |
| RT6160 | 0x62 | Ajustează tensiunea de ieșire la nevoie |
| BMA423 | 0x18 | Furnizează date accelerometru și numără pași |
| MAX17048 | 0x36 | Raportează procentul de baterie și tensiunea celulei |
| DRV2605 | 0x5A | Selectează și redă efecte tactile |

Clock pe **P0.06**, date pe **P0.07**. Rezistoare de pull-up pe ambele linii.

---

### SPI — Display E-Paper

Comunicație unidirecțională spre display — nu există linie MISO deoarece displayul nu transmite date înapoi. Frecvență maximă 4MHz. Pe lângă SCK și MOSI, display-ul necesită 3 semnale GPIO suplimentare: **DC** (Data/Command), **RST** (reset hardware) și **BUSY** (MCU-ul așteaptă când displayul procesează un refresh).

---

### SWD — Programare și debug

Conectorul **TC2030-IDC** (Tag-Connect, fără pini fizici pe PCB) expune SWDIO, SWDCLK, SWO și RESET. Nu ocupă înălțime în ansamblu — cablul de programare se atașează temporar prin spring pins pe pad-urile PCB-ului.

---

### Bluetooth și antenă

Antena **2450AT18B100E** este o antenă SMD ceramică pentru 2.4GHz, plasată spre exteriorul plăcii cu un decupaj în substrat sub ea. Decupajul elimină FR4-ul (material cu pierderi dielectrice la 2.4GHz) din zona de radiație, îmbunătățind câștigul și adaptarea de impedanță a antenei. Cristalul de 32MHz furnizează referința de frecvență pentru modulul radio.

---

### Cristalul de 32.768kHz — RTC în sleep

Al doilea oscilator alimentează timer-ul RTC al nRF52840 și rămâne activ inclusiv în deep sleep. Permite dispozitivului să mențină ora exactă și să se trezească la intervale precise fără a ține procesorul activ.

---

### BMA423 — Mai mult decât un accelerometru

BMA423 dispune de un procesor intern dedicat pentru algoritmii de step counting și wake-on-wrist, care rulează independent de nRF52840. La detectarea mișcării specifice ridicării mâinii, trimite o întrerupere pe **P1.08 (IMU_INT1)** care trezește MCU-ul din deep sleep în microsecunde. Consum în această stare: 0.9μA.

---

### Estimare consum și autonomie

| Scenariu | Curent estimat |
|---|---|
| nRF52840 TX Bluetooth activ | 6.26 mA |
| nRF52840 RX Bluetooth activ | 4.82 mA |
| Refresh display E-Paper | ~26 mA (câteva secunde) |
| BMA423 monitorizare continuă | 130 μA |
| MAX17048 fuel gauge activ | 23 μA |
| DRV2605 vibrație activă | 5 mA |
| nRF52840 deep sleep + RTC + BMA low-power | ~50–100 μA |

Consum mediu în utilizare normală (BT conectat periodic, refresh display la fiecare minut, IMU activ): **6–8 mA la 3.3V**.

Cu o baterie LiPo de **300mAh**: autonomie estimată **35–50 ore**.

---

## Alocarea pinilor nRF52840

### Display E-Paper (SPI)

| Pin MCU | Semnal | Direcție | Descriere |
|---|---|---|---|
| P0.02 / AIN0 | SCK | OUT | Clock SPI pentru display |
| P0.03 / AIN1 | MOSI | OUT | Date seriale spre display |
| P0.05 / AIN3 | EPD_CS | OUT | Chip select, activ LOW |
| P0.15 | EPD_RST | OUT | Reset hardware display, activ LOW |
| P0.16 | EPD_DC | OUT | LOW = comandă, HIGH = date |
| P0.17 | EPD_BUSY | IN | HIGH când displayul procesează |

### Periferice I2C

| Pin MCU | Semnal | Descriere |
|---|---|---|
| P0.06 | SCL | Clock comun pentru toate perifericele I2C |
| P0.07 | SDA | Date bidirecționale I2C |
| P0.08 | IMU_INT2 | Întrerupere secundară BMA423 (step counter) |
| P1.08 | IMU_INT1 | Întrerupere primară BMA423 (wake-on-wrist) |
| P0.10 / NFC2 | FUELGAUGE_ALT | Alarmă MAX17048 la baterie scăzută |
| P0.11 | CHARGER_PG | Power Good BQ25180 — HIGH când USB e valid |
| P0.12 | HAPTIC_EN | Enable DRV2605, activ HIGH |

### Butoane

| Pin MCU | Semnal | Descriere |
|---|---|---|
| P0.13 | BTN_UP | Buton sus (navigare meniu) |
| P0.14 | BTN_ENT | Buton enter / confirmare |
| P1.02 | BTN_DN | Buton jos (navigare meniu) |

### USB, SWD și ceasuri

| Pin MCU | Semnal | Interfață | Descriere |
|---|---|---|---|
| D+ | USB_DP | USB | Linie diferențială pozitivă, prin ESD |
| D− | USB_DM | USB | Linie diferențială negativă, prin ESD |
| VBUS | 5V_USB | PWR | Detecție USB prezent → BQ25180 |
| SWDIO | SWDIO | SWD | Date programare/debug |
| SWDCLK | SWDCLK | SWD | Clock programare/debug |
| SWO | SWO | SWD | Serial Wire Output (trace) |
| P0.18 / RESET | RESET | GPIO | Reset hardware MCU |
| ANT | RF | RF | Antenă Bluetooth 2.4GHz |
| XC1, XC2 | XTAL_32M | CLK | Cristal 32MHz — clock principal |
| XL1, XL2 | XTAL_32K | CLK | Cristal 32.768kHz — RTC sleep |

---

## PCB — Decizii de proiectare

### 4 straturi

| Strat | Conținut |
|---|---|
| L1 — Top | Toate componentele SMD + rutare semnale principale |
| L2 | Plan de masă GND continuu |
| L3 | Plan de alimentare 3V3 |
| L4 — Bottom | Rutare secundară + conexiuni via |

Planul de masă pe L2 direct sub stratul de semnal L1 minimizează inductanța buclei de return. Planul de 3V3 pe L3 reduce impedanța de alimentare, esențial pentru vârfurile de curent ale nRF52840 în modul TX.

### Decupaj sub antenă

FR4-ul este un material cu pierderi dielectrice la 2.4GHz. Un decupaj fizic al PCB-ului sub jumătatea neconectată a antenei 2450AT18B100E permite acesteia să radieze în aer, îmbunătățind performanța RF.

### Test pad-uri

14 test pad-uri distribuite pe semnalele: 3V3, VBAT, GND, SDA, SCL, SWDCLK, SWDIO, SWO, RESET, VREG. Permit conectarea unui multimetru sau logic analyzer după asamblare.

### Erori acceptate

- **Board Outline Clearance** — erorile generate de markerii de dimensiuni și textele de silkscreen aflate lângă marginea plăcii au fost aprobate, deoarece nu afectează fabricabilitatea PCB-ului.
- **Copper Width** pe traseele de sub componentele BGA — lățimea a fost redusă local pentru a permite rutarea prin pitch-ul mic al pad-urilor BGA, apoi adusă înapoi la 0.3mm după ieșirea din zona BGA.

---

## Ansamblu 3D

Modelat complet în Autodesk Fusion 360. Stratificarea de jos în sus:

```
┌─────────────────────────┐  ← Cadru superior + geam display
│     Display E-Paper     │  ← Panou e-ink + cablu FPC
│         PCB             │  ← Placa cu toate componentele
│      Baterie LiPo       │  ← Celulă lipită direct pe test pad-uri
└─────────────────────────┘  ← Carcasă inferioară
```

Butoanele (×3) sunt accesibile prin locașurile laterale ale carcasei. Conectorul USB-C iese prin fanta frontală. Shaker-ul haptic este montat în spațiul rămas lângă baterie.

---

## Galerie

<img width="653" height="455" alt="image" src="https://github.com/user-attachments/assets/0d6f5b60-5c2c-45fb-a187-d6ea14b3ca67" />
<img width="809" height="540" alt="image" src="https://github.com/user-attachments/assets/85f2c10c-b60a-41dd-9321-0ca004e58729" />
<img width="887" height="610" alt="image" src="https://github.com/user-attachments/assets/d41584c5-f2f7-450d-882f-9d15de4f34ec" />
<img width="803" height="471" alt="image" src="https://github.com/user-attachments/assets/4c22e02f-d64b-402c-a0d9-f03ea3e4e572" />




---

## Structura repository-ului

```
InkTime/
│
├── Hardware/
│   ├── PCB.brd                  # Fișier board Fusion Electronics
│   ├── Schematic_Final.sch      # Schematic complet
│   └── Schematic_Final.pdf      # Export PDF schematic
│
├── Manufacturing/
│   ├── gerbers.zip              # Fișiere Gerber pentru fabricație
│   ├── InkTime.bom              # Bill of Materials
│   └── PnP_InkTime_front.cpl   # Pick & Place față
│
├── Mechanical/
│   ├── Exploded_Watch3D.zip    # Exported STEP ansamblu exploded view
│   └── Assembled Watch.f3z           # Proiect Autodesk Fusion 360
│
├── Images/                      # Randări și capturi PCB
│
├── LICENSE
└── README.md
```

---

MIT License — Copyright 2026 Rusu David Gabriel
