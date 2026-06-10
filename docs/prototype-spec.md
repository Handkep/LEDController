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

## Versorgungen

| Netz | Quelle | Verbraucher / Nutzung | Anforderungen |
| --- | --- | --- | --- |
| `+24V` | Externer Eingang | RGBW-Ausgang, Buck-Regler | Stromlimit festlegen |
| `+3.3V` | `U1` TPS54202DDC | ESP32, Pull-ups, Logik | stabil beim WLAN-Betrieb |
| `+5V` | derzeit Kontext für Level-Shifter / AC-Power-Idee | LED-Daten-Level-Shifter | Quelle final klären |
| `GND` | Externer Eingang | Gesamtes Niedervolt-System | durchgehend, stern-/flächenfähig |

Konkrete Anforderungen:

- 3.3V-Schiene für ESP32-Spitzenströme auslegen.
- Bulk-Kapazität an 24V-Eingang und LED-Ausgängen.
- 100nF und 10µF lokal an MCU, Buck-Regler und Level-Shiftern.
- `+5V`-Strategie final klären: externe Einspeisung, interne Erzeugung oder nur Referenz/Weiterleitung.
- min. 4 Tür sensoren (nc)

## Digitale LED-Ausgänge

| Block | Bauteile | Kanäle | Ziel |
| --- | --- | --- | --- |
| Level-Shifter | `U4`, `U5`, `U6` SN74LVC2T45DCUR | 6 | 3.3V auf 5V |
| Serienwiderstände | `R13`-`R18` | 6 | Signalberuhigung |
| Stecker | `J11`, `J12` | 3 + 3 | externe LED-Datenleitungen |

Anforderungen:

- Ausgangspegel: 5V-kompatibel.
- Serienwiderstand: 100Ω je Datenleitung.
- pro Kanal 1 Datenleitung + GND + 24V
- Master Relais zum Abschalten der LED-Spannung zum Stromsparen


ts mit Beschriftung/Silkscreen abgleichen.


## Optional / Offen: 230V-AC-Power

Nicht freigegeben. Nur als Idee im Projekt vorhanden.

- Soll 230V überhaupt auf dieses PCB?
- Schutzklasse, Erdung, Berührschutz.
- Kriech- und Luftstrecken.
- Sicherung und Überspannungsschutz.
- Gehäuse und Zugentlastung.
- Zulassung / Normen.
- Alternative: externes zertifiziertes Netzteil.
