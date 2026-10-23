# Software (ESPHome)

Die Firmware der Imker-Waage basiert auf [ESPHome](https://esphome.io/) und wird über
eine YAML-Konfiguration beschrieben. Alle Dateien liegen in diesem Ordner.

| Datei                     | Inhalt                                                        |
| ------------------------- | ------------------------------------------------------------- |
| `smarterBienenstock.yaml` | Haupt-Konfiguration (Sensoren, Kalibrierung, ThingSpeak, LED) |
| `fft_include.h`           | Include für die FFT-Bibliothek (`#include <arduinoFFT.h>`)    |
| `arduinoFFT.h`            | Kopie der arduinoFFT-Bibliothek (Frequenzanalyse Mikrofon)    |

## Eckdaten der Konfiguration

- **Board:** `az-delivery-devkit-v4` (ESP32 DevKit V4), Framework `arduino`
- **Gerätename / Hotspot:** `smartebeute`, erreichbar unter `http://smartebeute.local`
- **Weboberfläche:** `web_server` v3, Konfiguration per **Improv** / **Captive Portal**
- **Updates:** OTA über WLAN
- **arduinoFFT:** wird über `libraries:` aus
  `https://github.com/kosme/arduinoFFT.git#v1.6.2` eingebunden
- **Projekt:** `esphome.project` ist auf `emc2saar.smartebeute` gesetzt (für das
  ESP-Web-Tools-Manifest)

## Bauen & Flashen ohne lokale Installation

Die Firmware muss **nicht** lokal kompiliert werden:

- **Cloud-Build:** Die GitHub Action `.github/workflows/deploy-pages.yml` baut die Binary
  per [`esphome/build-action`](https://github.com/esphome/build-action) automatisch und
  legt sie zusammen mit einem ESP-Web-Tools-`manifest.json` unter `firmware/` auf der
  GitHub-Pages-Seite ab.
- **Browser-Flashen:** Auf der Projektseite (`index.html`) steht ein
  [ESP Web Tools](https://esphome.github.io/esp-web-tools/)-Button. Damit lässt sich die
  Waage per Web Serial (Chrome/Edge, HTTPS) ohne jede Installation flashen.
- **Lokal/Selbst bauen:** Alternativ `smarterBienenstock.yaml` mit ESPHome kompilieren
  (CLI, Home-Assistant-Add-on oder ESPHome Web Installer).

## Pin-Belegung (laut Konfiguration)

| Funktion                     | Pin / Bus                        |
| ---------------------------- | -------------------------------- |
| I²C Hauptplatine (Bus 1)     | SDA `GPIO19`, SCL `GPIO18`       |
| I²C Umgebung (Bus 2)         | SDA `GPIO25`, SCL `GPIO26`       |
| 1-Wire 1 (DS18B20)           | `GPIO4`                          |
| 1-Wire 2 (DS18B20)           | `GPIO27`                         |
| HX711 (Waage)                | DOUT `GPIO39`, CLK `GPIO23`      |
| Mikrofon (ADC)               | `GPIO34`                         |
| Service-Schalter             | `GPIO35`                         |
| Status-LED (WS2812)          | `GPIO33`                         |

## Sensoren & I²C-Adressen

| Sensor   | Messgröße                         | Bus / Adresse        |
| -------- | --------------------------------- | -------------------- |
| BME280   | Temp./Feuchte/Druck innen         | Bus 1, `0x76`        |
| BMP280   | Temp./Druck Platine               | Bus 1, `0x77`        |
| BMP280   | Temp./Druck Umgebung              | Bus 2, `0x76`        |
| BH1750   | Helligkeit                        | Bus 2, `0x23`        |
| INA219   | Strom, Leistung, Busspannung      | Bus 1, `0x40`        |
| 2× DS18B20 | Temperatur innen 01/02          | 1-Wire (GPIO4/GPIO27)|
| HX711    | Rohwert Waage1 → Gewicht          | GPIO39/GPIO23        |
| Mikrofon | Frequenz (FFT, 128 Samples)       | ADC GPIO34           |

> Hinweis: Die Konfiguration nutzt aktuell **einen** HX711-Wägekanal (`Waage1`). Die
> Hauptplatine ist für **zwei** HX711 ausgelegt; ein zweiter Kanal kann analog ergänzt werden.

## Status-LED (WS2812)

| Farbe        | Bedeutung                                  |
| ------------ | ------------------------------------------ |
| Grün         | Datenübertragung erfolgreich               |
| Gelb         | Fehler bei der Übertragung                 |
| Rot          | Service-Schalter aktiv (Wartung)           |

## ThingSpeak

Die Waage sendet über zwei Channels (Beute + Umgebung). Die **Write-Keys** werden in der
Weboberfläche gesetzt (`Thinkspeak WriteKey Beute` / `Thinkspeak WriteKey Umgebung`).

**Channel „Beute"**: field1 Gewicht · field2 Frequenz · field3 Temp. innen ·
field4 Luftfeuchte innen · field5 Temp. Brut · field6 Temp. Honig

**Channel „Umgebung"**: field1 Helligkeit · field2 Außentemperatur · field3 Außendruck ·
field5 Busspannung · field6 Leistung · field7 Temp. Platine

## Kalibrierung (Waage1)

Über die Weboberfläche:

1. Ohne Last den Button **„Waage1 setzte 0kg"** drücken.
2. Mit bekanntem Gewicht (10 kg) den Button **„Waage1 setzte 10kg"** drücken.

Daraus berechnet die Vorlage `Waage1 Gewicht` die lineare Umrechnung (die Werte stehen in
`Waage1 bei 0kg` / `Waage1 bei 10kg` und werden dauerhaft gespeichert).
