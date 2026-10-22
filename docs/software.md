# Software (ESPHome)

Die Firmware des ESP32 basiert auf **[ESPHome](https://esphome.io/)**. Dadurch wird die
Waage über eine einfache YAML-Konfiguration beschrieben und lässt sich bequem per OTA
(Over-the-Air) aktualisieren. Die Datenübertragung erfolgt über **WLAN** und **LoRa**.

> 📦 **Software und Flash-Anleitung auf GitHub:** Die Firmware (ESPHome) liegt in einem
> GitHub-Repository. Dort ist auch beschrieben, wie die Software mit dem **ESP Web Flasher**
> aufgespielt wird. **Repository-Link:** *(wird noch nachgereicht)*.

## Voraussetzungen

- Der ESP32 ist aufgesteckt und wird korrekt versorgt (3,3 V am Messpunkt geprüft, siehe
  [Stromversorgung](stromversorgung.md#6-esp32-aufstecken-und-33-v-prüfen)).
- Ein Rechner mit einem Browser, der **Web Serial** unterstützt (z. B. Chrome oder Edge) –
  für den **ESP Web Flasher**.
- Ein **USB-Kabel** zum **ESP32-DevKit**. Geflasht wird über die **USB-Buchse am
  ESP32-DevKit** selbst.

> ⚠️ **Nicht verwechseln:** Zum Flashen wird die **USB-Buchse des ESP32-DevKits** verwendet –
> **nicht** die USB-A-Buchse auf der Platine. Die USB-A-Buchse der Platine ist nur ein
> **5-V-Ausgang** (keine Datenverbindung).

## Erstes Flashen (ESP Web Flasher)

Das erste Aufspielen der Firmware erfolgt über den **ESP Web Flasher** direkt aus dem
Browser. Die genaue Schritt-für-Schritt-Anleitung steht im GitHub-Repository.

1. Den Rechner per **USB-Kabel mit der USB-Buchse des ESP32-DevKits** verbinden.
2. Das **GitHub-Repository** öffnen (Link siehe oben, *wird noch nachgereicht*).
3. Der dortigen Anleitung zum **ESP Web Flasher** folgen (Firmware im Browser auswählen und
   auf den ESP32 flashen).
4. Nach dem ersten Flashen startet die Waage einen **WLAN-Hotspot** zur Erstkonfiguration
   (siehe nächster Abschnitt).

> **Noch zu ergänzen:** GitHub-Repository-Link und Name/Variante der Firmware.

## Erstkonfiguration über den Hotspot

Nach dem Flashen **startet der ESP32 einen eigenen WLAN-Hotspot**. Darüber wird die Waage
das erste Mal eingerichtet (z. B. WLAN-Zugangsdaten hinterlegen).

1. Mit **Laptop oder Handy** nach WLAN-Netzen suchen.
2. Mit dem Hotspot verbinden:
   - **Netzwerkname (SSID):** `smartebeute`
   - **Passwort:** *kein Passwort* (offenes Netz)
3. Nach dem Verbinden öffnet sich ein **Portal**. Dort das **eigene WLAN auswählen**, mit
   dem sich die Waage verbinden soll, und die Zugangsdaten eingeben.
4. Die Waage verbindet sich anschließend mit dem gewählten WLAN und ist dann im **eigenen
   Netzwerk** über den Browser erreichbar unter:

   **<http://smartebeute.local>**

5. Sobald die Waage im WLAN ist, sind auch **OTA-Updates** über WLAN möglich.

> 💡 Erscheint das Portal nicht automatisch, im Browser eine beliebige Seite aufrufen –
> dann sollte das Portal (Captive Portal) erscheinen. Klappt `smartebeute.local` im
> Netzwerk nicht (mDNS), kann alternativ die vom Router vergebene **IP-Adresse** der Waage
> verwendet werden.

## Konfiguration (Überblick)

Die ESPHome-Konfiguration bindet u. a. folgende Komponenten ein:

- **Wägesensoren über HX711** – Gewichtsmessung (benötigt Kalibrierung, siehe unten)
- **BME280** (Deckel-Platine) – Temperatur, Luftfeuchte, Luftdruck (I²C)
- **BMP280** (Rückseite Hauptplatine) – Temperatur, Luftdruck (I²C)
- **INA219** – Spannung und Strom von Batterie und Solarzelle (I²C)
- **DS18B20** (bis zu 3×) – Temperatur (1-Wire)
- **Mikrofon** – Stockgeräusche *(Auswertung noch zu ergänzen)*
- **Helligkeitssensor** (3. Platine) *(noch zu ergänzen)*
- **WLAN** – Datenübertragung an **ThingSpeak** (siehe unten)
- **LoRa** – Datenübertragung über Funk *(Zielsystem, z. B. TTN, noch zu ergänzen)*

> Die konkreten Pins, Sensor-IDs und Adressen stehen in der projektspezifischen
> YAML-Konfiguration *(noch zu ergänzen)*.

## Wägezelle anschließen (4 Kabel)

Die Wägezelle hat **4 Kabel**, die an die Platine (HX711) angeschlossen werden. Die
zugehörigen **Schraubklemmen** sind mit **Waage1** und **Waage2** gekennzeichnet und sitzen
**in der Nähe des HX711**. **Welches Kabel an welchen Anschluss gehört, muss durch
Ausprobieren ermittelt werden.**

So prüft man, ob die Kabel richtig angeschlossen sind:

1. Die Waage im laufenden Betrieb beobachten und die **Rohdaten** der Waage ansehen.
2. **Gewicht auflegen.** Gehen die **Rohwerte nach oben**, sind die Kabel **richtig**
   angeschlossen.
3. Werden die Rohwerte beim Auflegen **kleiner** (oder reagieren gar nicht sinnvoll), die
   **Kabelbelegung ändern** und erneut testen.

> ℹ️ **Wichtig:** In diesem Schritt wird **noch kein korrektes Gewicht** angezeigt – es geht
> nur um die **Rohwerte**. Das richtige Gewicht ergibt sich erst nach der **Kalibrierung**
> (siehe unten).

### Beispiel-Kabelbelegung (eine konkrete Wägezelle)

> ⚠️ **Die Farbcodierung der Wägezellen ist nicht einheitlich** und unterscheidet sich
> leider oft von Hersteller zu Hersteller. Das folgende Beispiel gilt **nur für diese eine
> getestete Zelle** – immer durch Ausprobieren (steigende Rohwerte) bestätigen!

Bei einer von uns angeschlossenen Wägezelle war die Belegung **von oben nach unten**, beginnend
**an der LED**:

| Reihenfolge (an der LED beginnend) | Kabelfarbe |
| ---------------------------------- | ---------- |
| 1 (oben)                           | **Rot**    |
| 2                                  | **Schwarz**|
| 3                                  | **Weiß**   |
| 4 (unten)                          | **Blau**   |

> **Noch zu ergänzen:** wo die Rohwerte in der Oberfläche angezeigt werden.

## Kalibrierung der Waage

Die Gewichtsmessung über den HX711 muss **einmalig kalibriert** werden. Grundsätzlich
funktionieren **alle gängigen Wägezellen** mit der Platine – die Kalibrierung sorgt dafür,
dass die konkret eingebaute Zelle korrekt misst.

> Voraussetzung: Die Wägezelle ist korrekt angeschlossen (Rohwerte steigen bei Belastung,
> siehe [Wägezelle anschließen](#wägezelle-anschließen-4-kabel)).

In der Software ist dafür bereits eine **Kalibrierfunktion mit zwei Referenzgewichten**
vorhanden:

1. **0 kg (ohne Last):** Waage leer lassen und den Referenzpunkt **0 kg** erfassen.
2. **10 kg (bekanntes Gewicht):** ein **10-kg-Referenzgewicht** auflegen und diesen Punkt
   erfassen.
3. Die Software ermittelt aus beiden Punkten die lineare Umrechnung und speichert sie.

Danach zeigt die Waage das Gewicht korrekt an. Bei Bedarf (z. B. nach Umbau) kann die
Kalibrierung wiederholt werden.

> **Noch zu ergänzen:** genaue Bedienung der Kalibrierfunktion (wo/wie in der Oberfläche
> bzw. in ESPHome ausgelöst).

## Datenübertragung

- **WLAN:** Die Messwerte werden über WLAN an **ThingSpeak** gesendet (siehe unten).
- **LoRa:** für Standorte ohne WLAN; Reichweite über LoRa-Funk
  *(Gateway/Netzwerk, z. B. eigenes Gateway oder TTN, noch zu ergänzen)*.

## Daten an ThingSpeak senden

Die Waage sendet ihre Messwerte an **[ThingSpeak](https://thingspeak.com/)** – eine
Online-Plattform, auf der die Daten gespeichert und in Diagrammen angezeigt werden.

### Konto und Channels

1. Auf [thingspeak.com](https://thingspeak.com/) ein **kostenloses Konto** anlegen.
   Im kostenlosen Account stehen **4 Channels** zur Verfügung.
2. **Zwei Channels** anlegen:
   - einen Channel für die **Beute** (z. B. Gewicht),
   - einen Channel für die **Umgebungswerte** (z. B. Temperatur, Luftfeuchte, Luftdruck).
3. Von **jedem** dieser Channels den **Write-Key (API-Write-Key)** notieren – diese beiden
   Keys werden gleich in der ESP-Weboberfläche hinterlegt.

### Write-Keys in der ESP-Weboberfläche hinterlegen

Die ThingSpeak-**Write-Keys** werden über die **Weboberfläche der smartebeute** gesetzt
(erreichbar unter **<http://smartebeute.local>**, siehe
[Erstkonfiguration](#erstkonfiguration-über-den-hotspot)):

1. Die smartebeute-Weboberfläche im Browser öffnen.
2. Den **Write-Key des Beute-Channels** und den **Write-Key des Umgebungs-Channels**
   eintragen.

> ℹ️ **Kein Speichern-Knopf nötig:** Die Write-Keys sind **sofort nach dem Einfügen
> gespeichert**. Die Waage überträgt die Messwerte anschließend an die passenden
> ThingSpeak-Channels.

> **Noch zu ergänzen:** genaue Bezeichnung der Felder in der Oberfläche und welche einzelnen
> Messwerte (Felder) auf welchem Channel landen.
