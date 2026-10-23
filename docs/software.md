# Software (ESPHome)

Die Firmware des ESP32 basiert auf **[ESPHome](https://esphome.io/)**. Dadurch wird die
Waage über eine einfache YAML-Konfiguration beschrieben und lässt sich bequem per OTA
(Over-the-Air) aktualisieren. Die Datenübertragung erfolgt über **WLAN**.

> 📦 **Software in diesem Repository:** Die Firmware (ESPHome) liegt im Ordner
> [`software/`](../software/) – die Haupt-Konfiguration ist
> [`software/smarterBienenstock.yaml`](../software/smarterBienenstock.yaml), zusammen mit
> den Hilfsdateien `fft_include.h` und `arduinoFFT.h` (für die Frequenzanalyse des
> Mikrofons). Eine Übersicht (Pins, Adressen, LED-Codes) steht in
> [`software/README.md`](../software/README.md).

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

Es gibt zwei Wege. In **beiden Fällen ist keine lokale ESPHome-Installation nötig.**

### Variante A: Direkt im Browser flashen (einfachste Variante)

Die Firmware wird per GitHub Action automatisch gebaut und auf der Projektseite
(GitHub Pages) bereitgestellt. Dort gibt es einen **„Imker-Waage flashen"**-Button
(ESP Web Tools). Voraussetzung: ein Browser mit **Web Serial** (Chrome oder Edge am
Desktop) und eine **HTTPS**-Verbindung.

1. Den Rechner per **USB-Kabel mit der USB-Buchse des ESP32-DevKits** verbinden.
2. Auf der Projektseite den **Flash-Button** klicken und den seriellen Port auswählen.
3. Nach dem Flashen WLAN direkt im Dialog einrichten (Improv) oder über den Hotspot
   (siehe nächster Abschnitt).

### Variante B: Selbst kompilieren

Die ESPHome-Konfiguration [`software/smarterBienenstock.yaml`](../software/smarterBienenstock.yaml)
mit ESPHome kompilieren und flashen (CLI, Home-Assistant-Add-on oder ESPHome Web Installer).
Beim Kompilieren werden `fft_include.h`/`arduinoFFT.h` sowie die arduinoFFT-Bibliothek
automatisch eingebunden.

> ℹ️ **Cloud-Build:** Das Bauen der Binary übernimmt die GitHub Action
> `esphome/build-action` automatisch in der Cloud (siehe
> `.github/workflows/deploy-pages.yml`). Auch hierfür muss ESPHome **nicht lokal
> installiert** sein.

> ℹ️ Das Board ist in der Konfiguration als `az-delivery-devkit-v4` (ESP32 DevKit V4) mit
> dem Arduino-Framework hinterlegt, der Gerätename ist `smartebeute`.

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

Die ESPHome-Konfiguration ([`software/smarterBienenstock.yaml`](../software/smarterBienenstock.yaml))
bindet u. a. folgende Komponenten ein:

- **HX711** – Gewichtsmessung (`Waage1`, benötigt Kalibrierung, siehe unten)
- **BME280** (Deckel-Platine) – Temperatur, Luftfeuchte, Luftdruck (I²C, `0x76`)
- **BMP280** (Rückseite Hauptplatine) – Temperatur, Luftdruck (I²C, `0x77`)
- **BMP280** (Umgebungsplatine) – Temperatur, Druck (I²C Bus 2, `0x76`)
- **BH1750** (Umgebungsplatine) – Helligkeit (I²C Bus 2, `0x23`)
- **INA219** – Strom, Leistung und Busspannung (I²C, `0x40`)
- **2× DS18B20** – Temperatur (1-Wire, `GPIO4` / `GPIO27`)
- **Mikrofon** – Frequenzanalyse per FFT (ADC `GPIO34` → Sensor „Frequenz")
- **Status-LED** (WS2812, `GPIO33`) – signalisiert den Übertragungsstatus
- **WLAN** – Datenübertragung an **ThingSpeak** (siehe unten)

> Die vollständige Pin-Belegung, die I²C-Adressen und die LED-Farbcodes sind in
> [`software/README.md`](../software/README.md) zusammengefasst.

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

> 💡 Die Rohwerte stehen in der Weboberfläche beim Sensor **„Waage1"** (der kalibrierte
> Wert erscheint separat als **„Waage1 Gewicht"**).

## Kalibrierung der Waage

Die Gewichtsmessung über den HX711 muss **einmalig kalibriert** werden. Grundsätzlich
funktionieren **alle gängigen Wägezellen** mit der Platine – die Kalibrierung sorgt dafür,
dass die konkret eingebaute Zelle korrekt misst.

> Voraussetzung: Die Wägezelle ist korrekt angeschlossen (Rohwerte steigen bei Belastung,
> siehe [Wägezelle anschließen](#wägezelle-anschließen-4-kabel)).

In der Weboberfläche (`http://smartebeute.local`) stehen dafür zwei **Buttons** und zwei
**Referenzwerte** bereit:

1. **0 kg (ohne Last):** Waage leer lassen und den Button **„Waage1 setzte 0kg"** drücken.
2. **10 kg (bekanntes Gewicht):** ein **10-kg-Referenzgewicht** auflegen und den Button
   **„Waage1 setzte 10kg"** drücken.
3. Aus den beiden gespeicherten Rohwerten (`Waage1 bei 0kg` / `Waage1 bei 10kg`) berechnet
   der Template-Sensor **„Waage1 Gewicht"** die lineare Umrechnung.

Danach zeigt die Waage das Gewicht korrekt an. Die Referenzwerte werden dauerhaft
gespeichert; bei Bedarf (z. B. nach Umbau) kann die Kalibrierung wiederholt werden.

## Datenübertragung

- **WLAN:** Die Messwerte werden über WLAN an **ThingSpeak** gesendet (siehe unten).

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

### Feldbelegung der Channels

Die Firmware sendet im Abstand von rund einer Minute an beide Channels:

| Feld   | Channel „Beute"         | Channel „Umgebung"     |
| ------ | ----------------------- | ---------------------- |
| field1 | Gewicht                 | Helligkeit             |
| field2 | Frequenz (Mikrofon-FFT) | Außentemperatur        |
| field3 | Temperatur innen        | Außendruck             |
| field4 | Luftfeuchte innen       | –                      |
| field5 | Temperatur Brut         | Busspannung (INA219)   |
| field6 | Temperatur Honig        | Leistung (INA219)      |
| field7 | –                       | Temperatur Platine     |
