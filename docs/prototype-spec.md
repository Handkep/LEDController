# LEDController Prototyp-Spezifikation

## Ziel

ESP32-S3-basierter Controller für 24V-LED-Lasten.

Ziel der PCB-Revision:

- 4 PWM-Kanäle für analoge 24V-RGBW-LEDs.
- 6 gepufferte/level-geshiftete Datenkanäle für adressierbare LEDs.
- USB-C, UART und JTAG für Programmierung und Debugging.
- Erweiterungsheader für freie ESP32-GPIOs.
- 24V-Eingang mit lokalem 3.3V-Buck-Regler.
- 230V/AC-Power nur als offene Option dokumentieren, nicht als freigegebenen Bestandteil.

## Projektstand

| Bereich | Stand |
| --- | --- |
| Hauptprojekt | `KiCad/LEDController.kicad_pro` |
| Hauptschaltplan | `KiCad/LEDController.kicad_sch` |
| PCB | `KiCad/LEDController.kicad_pcb` |
| Digitale Ausgänge | `KiCad/outputs.kicad_sch` |
| AC-Power-Idee | `KiCad/AC_Power.kicad_sch` |
| Produktionsdaten | `KiCad/production/`, `KiCad/dfm/gerber/` |
| Panel | `KiCad/panel.kicad_pcb`, `KiCad/panel.kicad_pro` |
| DRC | `KiCad/DRC.rpt`, aktuell 12 Violations |

## Kernbaugruppen

| Baugruppe | Referenzen | Ziel / Funktion | Status |
| --- | --- | --- | --- |
| MCU | `U2` ESP32-S3-WROOM-2 | WiFi/BLE-MCU, LED-Steuerung, USB, UART, JTAG | Muss |
| Buck-Regler | `U1` TPS54202DDC | 24V auf 3.3V | Muss |
| USB-C | `J6` | USB.D+/USB.D- zum ESP32 | Muss, CC/VBUS/Shield prüfen |
| UART | `J1` | Serielles Debug/Flash | Muss |
| JTAG | `J8` | Hardware-Debugging | Muss |
| GPIO-Header | `J7` | Erweiterung / Breakout | Muss |
| RGBW-Ausgang | `J4` | 24V + 4 Low-Side-Kanäle | Muss |
| MOSFETs | `Q1`-`Q4` IRLZ44N | Low-Side-Schalter für RGBW | Muss, Strom/Thermik offen |
| Digitale Daten | `J11`, `J12`, `U4`-`U6` | 6 Datenkanäle mit 5V-Level-Shifting | Muss |
| Status-LED | `D1`, `R8` | Firmware-/Board-Status | Muss |
| Bedienung | `SW1`, `SW2` | BOOT und EN/RESET | Muss |
| Montage | `H1`-`H4` | M3-Montagelöcher | Muss |
| AC-Power | `AC_Power`-Sheet | 230V-Eingang, 5V-Netzteil, Relais | Offen / nicht freigegeben |

## Versorgungen

| Netz | Quelle | Verbraucher / Nutzung | Anforderungen |
| --- | --- | --- | --- |
| `+24V` | Externer Eingang | RGBW-Ausgang, Buck-Regler | Stromlimit festlegen |
| `+3.3V` | `U1` TPS54202DDC | ESP32, Pull-ups, Logik | stabil beim WLAN-Betrieb |
| `+5V` | derzeit Kontext für Level-Shifter / AC-Power-Idee | LED-Daten-Level-Shifter | Quelle final klären |
| `GND` | Externer Eingang | Gesamtes Niedervolt-System | durchgehend, stern-/flächenfähig |

Konkrete Anforderungen:

- 24V-Eingang mit ausreichend Stromreserve für alle LED-Kanäle.
- 3.3V-Schiene für ESP32-Spitzenströme auslegen.
- Bulk-Kapazität an 24V-Eingang und LED-Ausgängen.
- 100nF und 10µF lokal an MCU, Buck-Regler und Level-Shiftern.
- `+5V`-Strategie final klären: externe Einspeisung, interne Erzeugung oder nur Referenz/Weiterleitung.

## Stecker und Pinouts

### `J1` UART

| Pin | Netz | Funktion |
| --- | --- | --- |
| 1 | `UART.TX` | ESP32 TX |
| 2 | `UART.RX` | ESP32 RX |

Offen: Ob zusätzlich GND am UART-Header benötigt wird.

### `J4` RGBW 24V-Ausgang

| Pin | Netz | Funktion |
| --- | --- | --- |
| 1 | `W_out` | Weiß-Kanal, geschalteter Low-Side-Ausgang |
| 2 | `B_out` | Blau-Kanal, geschalteter Low-Side-Ausgang |
| 3 | `R_out` | Rot-Kanal, geschalteter Low-Side-Ausgang |
| 4 | `G_out` | Grün-Kanal, geschalteter Low-Side-Ausgang |
| 5 | `+24V` | gemeinsame LED-Versorgung |

Offen: Reihenfolge mechanisch gegen Gehäuse/Kabelbaum prüfen. Pin 1 = `W_out` ist aktuell aus PCB abgeleitet.

### `J7` GPIO-Erweiterungsheader

| Pin | Netz | Funktion / Status |
| --- | --- | --- |
| 1 | `+3.3V` | Versorgung |
| 2 | `GND` | Masse |
| 3 | `GPIO.8` | ESP32-GPIO |
| 4 | unconnected | offen |
| 5 | unconnected | offen |
| 6 | unconnected | offen |
| 7 | unconnected | offen |
| 8 | unconnected | offen |
| 9 | unconnected | offen |
| 10 | `GPIO.21` | ESP32-GPIO |
| 11 | `GPIO.38` | ESP32-GPIO |
| 12 | `GPIO.45` | ESP32-GPIO |
| 13 | `GPIO.46` | ESP32-GPIO |
| 14 | `GPIO.47` | ESP32-GPIO |

Offen: Entscheiden, ob Pins 4-9 wirklich leer bleiben oder weitere GPIOs/I2C dort hin sollen.

### `J8` JTAG

| Pin | Netz | Funktion |
| --- | --- | --- |
| 1 | `JTAG.MTCK` | JTAG clock |
| 2 | `JTAG.MTDO` | JTAG data out |
| 3 | `JTAG.MTDI` | JTAG data in |
| 4 | `JTAG.MTMS` | JTAG mode select |

Offen: GND/3.3V am Debug-Port fehlen aktuell als eigene Pins.

### `J3` 24V / VDD-Terminal

| Pin | Netz | Funktion |
| --- | --- | --- |
| 1 | `+24V` | Eingang / Verteilung |
| 2 | `+24V` | Eingang / Verteilung |
| 3 | `+24V` | Eingang / Verteilung |

Hinweis: Referenz/Value im Projekt ist uneinheitlich (`GND`/VDD-Kontext prüfen). Pinout im PCB zeigt alle drei Pins auf `+24V`.

### `J5` 3-poliger JST-XH

| Pin | Netz | Funktion / Status |
| --- | --- | --- |
| 1 | `Net-(J5-Pin_1)` | offen |
| 2 | `Net-(J5-Pin_2)` / `Data` über Serienwiderstand | Daten-/Option-Leitung |
| 3 | `GND` | Masse |

Offen: Ziel dieses Steckers konkret festlegen oder entfernen.

### `J11` LED_Data

| Pin | Netz | ESP32-Seite | Funktion / Status |
| --- | --- | --- | --- |
| 1 | `Net-(J11-Pin_1)` | `GPIO.11` über 100Ω | Datenkanal |
| 2 | `Net-(J11-Pin_2)` | `GPIO.12` über 100Ω | Datenkanal |
| 3 | `Net-(J11-Pin_3)` | `GPIO.13` über 100Ω | Datenkanal |

Offen: Terminalblock führt aktuell drei Datenpins, keinen GND/5V-Pin. Externe Masseführung dokumentieren.

### `J12` LED_Data

| Pin | Netz | ESP32-Seite | Funktion / Status |
| --- | --- | --- | --- |
| 1 | `Net-(J12-Pin_1)` | `GPIO.14` über 100Ω | Datenkanal |
| 2 | `Net-(J12-Pin_2)` | `GPIO.17` über 100Ω | Datenkanal |
| 3 | `Net-(J12-Pin_3)` | `GPIO.18` über 100Ω | Datenkanal |

Offen: Terminalblock führt aktuell drei Datenpins, keinen GND/5V-Pin. Externe Masseführung dokumentieren.

### `J6` USB-C

| Signal | Netz | Status |
| --- | --- | --- |
| D+ | `USB.D+` | zum ESP32 |
| D- | `USB.D-` | zum ESP32 |
| GND | `GND` | angeschlossen |
| VBUS | unconnected | prüfen |
| CC1/CC2 | unconnected | prüfen |
| SBU1/SBU2 | unconnected | okay, wenn nicht genutzt |
| Shield | unconnected | ESD/EMV-Konzept prüfen |

DRC meldet Soldermask-Bridges an USB-C-Pads. Vor Fertigung klären.

## MCU-Signale

| Netz | Verwendung |
| --- | --- |
| `R`, `G`, `B`, `W` | MOSFET-Gates für RGBW-Kanäle |
| `Data` | separater Daten-/Option-Kanal zu `J5` |
| `GPIO.11`, `GPIO.12`, `GPIO.13` | Datenkanäle zu `J11` |
| `GPIO.14`, `GPIO.17`, `GPIO.18` | Datenkanäle zu `J12` |
| `GPIO.6` | Status-LED über `R8`/`D1` |
| `I²C.SCL`, `I²C.SDA` | I²C, Pull-ups vorhanden |
| `USB.D+`, `USB.D-` | USB-C |
| `UART.TX`, `UART.RX` | UART-Header |
| `JTAG.MTCK`, `JTAG.MTDO`, `JTAG.MTDI`, `JTAG.MTMS` | JTAG-Header |
| `ESP_BOOT` | BOOT-Taster |
| `ESP_EN` | Enable/Reset-Taster |

Offen: ESP32-Strapping-Pins gegen Beschaltung und Boot-Modi prüfen.

## Analoge 24V-LED-Ausgänge

| Kanal | MOSFET | Gate-Netz | Ausgang | Pull-down | Serienwiderstand |
| --- | --- | --- | --- | --- | --- |
| R | `Q1` | `R` | `R_out` | 100k | 100Ω |
| G | `Q2` | `G` | `G_out` | 100k | 100Ω |
| B | `Q3` | `B` | `B_out` | 100k | 100Ω |
| W | `Q4` | `W` | `W_out` | 100k | 100Ω |

Noch festzulegen:

- Maximalstrom pro Kanal.
- Dauerstrom gesamt.
- MOSFET-Verlustleistung bei 3.3V Gate-Spannung.
- Leiterbahnbreite/Kupferfläche je Kanal.
- Kühlung / Gehäuseabstand.
- Absicherung der 24V-LED-Ausgänge.

## Digitale LED-Ausgänge

| Block | Bauteile | Kanäle | Ziel |
| --- | --- | --- | --- |
| Level-Shifter | `U4`, `U5`, `U6` SN74LVC2T45DCUR | 6 | 3.3V auf 5V |
| Serienwiderstände | `R13`-`R18` | 6 | Signalberuhigung |
| Stecker | `J11`, `J12` | 3 + 3 | externe LED-Datenleitungen |

Anforderungen:

- Ausgangspegel: 5V-kompatibel.
- Serienwiderstand: 100Ω je Datenleitung.
- Datenrate: kompatibel zu adressierbaren LEDs, z. B. WS281x/SK6812; genaue LED-Familie festlegen.
- Gemeinsame Masse mit LED-Last sicherstellen.

Offen: Ob pro Stecker zusätzlich GND/+5V mitgeführt werden soll. Aktueller Stand führt primär Datenleitungen.

## Mechanik und Fertigung

| Thema | Ziel / Stand |
| --- | --- |
| PCB-Lagen | 4 Layer |
| Montage | 4x M3-Montageloch (`H1`-`H4`) |
| USB-C | Randnah, mechanisch zugänglich |
| Leistung | TO-220-MOSFETs mit thermischem Abstand |
| Panel | Panel-Dateien vorhanden |
| Fertigungsdaten | Gerber/Drill/ZIP vorhanden |

Vor Fertigung:

- Schaltplan-ERC ausführen.
- PCB-DRC ohne ungeklärte Fehler.
- Gerber/Drill neu exportieren.
- BOM prüfen.
- Footprints gegen verfügbare Bauteile prüfen.
- Stecker-Pinouts mit Beschriftung/Silkscreen abgleichen.

## DRC-Status

Aktueller Report: `KiCad/DRC.rpt`, 2025-05-30.

| Typ | Anzahl / Ort | Aktion |
| --- | --- | --- |
| Courtyard-Overlap | `U2/H2`, `J1/J7`, `J8/J7` | Layoutabstände prüfen |
| Starved Thermal | GND-Zone an mehreren Pads | Thermal-Spokes korrigieren |
| Soldermask-Bridge | USB-C VBUS-Pads | USB-C-Footprint/Mask prüfen |
| Unconnected Pads | 0 | okay |

## Optional / Offen: 230V-AC-Power

Nicht freigegeben. Nur als Idee im Projekt vorhanden.

| Bauteil | Funktion | Status |
| --- | --- | --- |
| `J9` | 230V-Eingang + Earth | offen |
| `F1` 0.5A | Sicherung | offen |
| `RV1` DGCX07D561K | Varistor | offen |
| `PS1` HLK-5M05 | 230V AC auf 5V DC | offen |
| `K1` Relais | 230V-Schalten | offen |
| `J10` | 230V-Ausgang | offen |

Vor Integration zwingend klären:

- Soll 230V überhaupt auf dieses PCB?
- Schutzklasse, Erdung, Berührschutz.
- Kriech- und Luftstrecken.
- Sicherung und Überspannungsschutz.
- Gehäuse und Zugentlastung.
- Zulassung / Normen.
- Alternative: externes zertifiziertes Netzteil.

## Offene Entscheidungen

| ID | Thema | Entscheidung nötig |
| --- | --- | --- |
| O-001 | Maximalstrom RGBW | Strom je Kanal und gesamt festlegen |
| O-002 | `+5V`-Quelle | intern, extern oder nur Referenz |
| O-003 | LED_Data-Pinout | nur Daten oder Daten + GND/+5V |
| O-004 | `J5` | Zweck festlegen oder entfernen |
| O-005 | UART | GND/3.3V am Header ergänzen? |
| O-006 | JTAG | GND/3.3V am Header ergänzen? |
| O-007 | `J7` Pins 4-9 | ungenutzt lassen oder belegen |
| O-008 | USB-C | CC/VBUS/Shield/ESD finalisieren |
| O-009 | 230V | aufnehmen oder dauerhaft auslagern |
| O-010 | MOSFET | IRLZ44N bei 3.3V Gate geeignet? |
| O-011 | Sicherung | 24V-Eingang und LED-Kanäle absichern? |
| O-012 | Gehäuse | Steckerpositionen und Montage prüfen |

## Bring-up-Checkliste

| Schritt | Test | Erwartung |
| --- | --- | --- |
| 1 | Sichtprüfung PCB | keine Lötbrücken, keine Bestückungsfehler |
| 2 | Kurzschlussprüfung | kein Kurzschluss zwischen `+24V`, `+3.3V`, `+5V`, `GND` |
| 3 | 24V mit Stromlimit einspeisen | kein hoher Ruhestrom |
| 4 | 3.3V messen | stabil, innerhalb Toleranz |
| 5 | Buck-Regler prüfen | keine Überhitzung, Ripple akzeptabel |
| 6 | USB verbinden | ESP32 wird erkannt |
| 7 | BOOT/EN testen | Bootloader/Reset funktionieren |
| 8 | Firmware flashen | erfolgreicher Flash über USB/UART |
| 9 | Status-LED testen | `GPIO.6` schaltet `D1` |
| 10 | JTAG testen | Debug-Verbindung stabil |
| 11 | RGBW ohne Last testen | Gates schalten korrekt |
| 12 | RGBW mit Testlast testen | Ausgänge schalten, MOSFETs bleiben kühl |
| 13 | LED_Data testen | 5V-Ausgangspegel, sauberes Signal |
| 14 | I²C testen | Pull-ups und Pegel korrekt |
| 15 | DRC/ERC erneut ausführen | keine ungeklärten Fehler |
