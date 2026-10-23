# Imker-Waage (Stockwaage) – Nachbau-Dokumentation

Dies ist die Nachbau-Dokumentation für unsere selbst entwickelte **Imker-Waage** (Stockwaage).
Sie richtet sich an **Imkerinnen und Imker, die die Waage selbst nachbauen** möchten.

Die Waage misst laufend das Gewicht eines Bienenstocks sowie weitere Umweltwerte und
überträgt die Daten per **WLAN**. Als Software kommt **ESPHome** zum Einsatz.

> ℹ️ Diese Doku entsteht gerade (u. a. während eines Imker-Workshops).
> Stellen, die noch nicht final sind, sind mit **„noch zu ergänzen"** markiert.

## Überblick

Die Waage besteht aus mehreren eigenen Platinen, die bei **JLCPCB** nachbestellt werden können:

- **Deckel-Platine** (im Deckel des Gehäuses): Mikrofon, Umgebungssensor **BME280**,
  Platz für bis zu **3× DS18B20** (Temperatur), Schraubklemme
- **Hauptplatine im Außengehäuse**: **ESP32**, **HX711** für zwei Wägesensoren,
  **BMP280** (Temperatur/Luftdruck) auf der Rückseite,
  **INA219** (Spannungs-/Strommessung Batterie & Solar),
  Anschluss für Stromversorgung (Akku/Netzteil und Solar)
- **Umgebungsplatine** (3. Platine): **BH1750** (Helligkeit) und ein weiterer
  Sensor-Steckplatz *(Details noch zu ergänzen)*

Die Platinen sind untereinander mit einem **RJ45-Kabel** verbunden.

| Eigenschaft        | Wert                                                    |
| ------------------ | ------------------------------------------------------- |
| Controller         | ESP32 DevKit V4                                         |
| Gewicht            | 2× Wägesensoren über HX711                               |
| Umwelt             | BME280 (Deckel: Temp./Luftfeuchte/Luftdruck), BMP280 (Hauptplatine: Temp./Luftdruck), bis 3× DS18B20, Mikrofon, Helligkeit |
| Datenübertragung   | WLAN (Daten an ThingSpeak)                              |
| Software           | ESPHome (eigene Konfiguration)                          |
| Stromversorgung    | Akku oder Netzteil 6–40 V, Solarmodul bis 30 W / max. 40 V |
| Platinen           | Eigenes Design, nachbestellbar bei JLCPCB               |
| Wetterschutz       | Über Außengehäuse (kein separater Diebstahl-/Manipulationsschutz) |

## Dokumentation

Die ausführliche Doku ist thematisch aufgeteilt im Ordner [`docs/`](docs/):

1. [Hardware & Platinen](docs/hardware.md) – Aufbau, Sensoren, ESP32, HX711, RJ45-Verbindung
2. [Platinen-Fertigung (JLCPCB)](docs/fertigung.md) – Bestellen und Bestücken der Platinen
3. [Stromversorgung](docs/stromversorgung.md) – Akku, Netzteil, Solar
4. [Software (ESPHome)](docs/software.md) – Flashen, Erstkonfiguration, ThingSpeak, Kalibrierung
5. [Montage & Betrieb](docs/montage-betrieb.md) – Aufbau am Bienenstock, Wetterschutz, Hinweise

Die zugehörigen Dateien liegen in diesem Repository:

- [`software/`](software/) – ESPHome-Konfiguration (`smarterBienenstock.yaml`) inkl.
  [Übersicht](software/README.md) (Pins, I²C-Adressen, LED-Codes, ThingSpeak-Felder)
- [`hardware/gerber/`](hardware/gerber/) – Gerber-Dateien je Platine (JLCPCB)
- [`hardware/schaltplan/`](hardware/schaltplan/) – Schaltpläne (PDF) je Platine
- [`hardware/bom/`](hardware/bom/) – Stückliste (`BOM-Bienenbeute.xlsx`)

## Status / noch zu klären

- [ ] Umgebungsplatine: zweiter Sensor-Steckplatz (neben BH1750) und Einbauort
- [x] Gerber-Dateien in `hardware/gerber/` abgelegt (je Platine)
- [x] BOM in `hardware/bom/` abgelegt (`BOM-Bienenbeute.xlsx`)
- [x] Schaltpläne in `hardware/schaltplan/` abgelegt (je Platine)
- [x] Firmware/ESPHome-Konfiguration in `software/` abgelegt
- [ ] Link zu weiteren Design-Dateien (Gehäuse)

## Hinweis: Kabelbelegung der Wägezelle

Die **Farbcodierung der Wägezellen ist nicht einheitlich** und unterscheidet sich oft je
nach Hersteller. Die richtige Belegung an **Waage1/Waage2** wird durch **Ausprobieren**
ermittelt – die Kabel stimmen, wenn die **Rohwerte bei Belastung steigen** (siehe
[Software → Wägezelle anschließen](docs/software.md#wägezelle-anschließen-4-kabel)).

Beispiel einer von uns getesteten Zelle (von oben nach unten, beginnend an der LED):
**Rot – Schwarz – Weiß – Blau**. Dies gilt **nur für diese eine Zelle** und ist kein
allgemeiner Standard.

## Hinweis zu Schutz / Sicherheit

Die Elektronik ist **nicht** durch zusätzliche Maßnahmen gegen Diebstahl oder Verschmutzung
gesichert. Der Schutz erfolgt ausschließlich über das **Außengehäuse**, das die Platinen vor
Witterung schützt. Siehe [Montage & Betrieb](docs/montage-betrieb.md).

## Haftungsausschluss

Diese Dokumentation wird **ohne Gewähr** bereitgestellt. Der **EmC² Saar e.V.** übernimmt
**keine Haftung** für Schäden, die aus dem Nachbau, dem Zusammenbau oder dem Betrieb der
Imker-Waage entstehen – insbesondere nicht für Sach-, Personen- oder Folgeschäden (z. B. an
Elektronik, Akkus, Bienenvölkern oder durch Arbeiten an der Stromversorgung).

Der **Nachbau und Betrieb erfolgt auf eigene Verantwortung und eigenes Risiko.** Für den
sach- und fachgerechten Aufbau, die Einhaltung der geltenden Vorschriften (z. B. im Umgang
mit Strom und Lötarbeiten) sowie die Sicherheit der verwendeten Komponenten ist allein die
nachbauende Person verantwortlich.

## Lizenz

Diese Dokumentation und das Hardware-Design (Texte, Bilder, Schaltpläne, Gerber-Dateien,
Stücklisten) stehen unter der Lizenz
**[CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.de)**
(Namensnennung – Nicht kommerziell – Weitergabe unter gleichen Bedingungen).

© 2026 **EmC² Saar e.V.** – siehe [`LICENSE`](LICENSE).

Eine **kommerzielle Nutzung ist nicht gestattet.** Bei Weitergabe oder Bearbeitung müssen
Namensnennung erfolgen und die gleichen Lizenzbedingungen beibehalten werden.

> Die ESP32-Firmware (ESPHome) liegt in einem separaten Repository und kann abweichend
> lizenziert sein.

## Förderung

Dieses Projekt wurde vom **Landkreis Sankt Wendel** im Rahmen von
**„Smart Wendeler Land"** gefördert.
