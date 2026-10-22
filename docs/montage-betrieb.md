# Montage & Betrieb

Dieser Abschnitt beschreibt den Aufbau der Waage am Bienenstock und worauf im laufenden
Betrieb zu achten ist.

## Mechanischer Aufbau

Die Waage misst das Gewicht des Bienenstocks über **zwei Wägesensoren** (ausgewertet über
den HX711 auf der Hauptplatine).

1. Wägesensoren gemäß Rahmen-/Waagenkonstruktion montieren, sodass der Stock gleichmäßig
   auf beiden Sensoren aufliegt. *(genaue Mechanik/Rahmen noch zu ergänzen)*
2. Die Wägezelle (**4 Kabel**) mit dem HX711 auf der Hauptplatine verbinden. Die richtige
   Belegung wird **durch Ausprobieren** ermittelt – die Kabel stimmen, wenn die **Rohwerte
   bei Belastung steigen** (siehe
   [Software → Wägezelle anschließen](software.md#wägezelle-anschließen-4-kabel)).
3. Deckel-Platine (mit BME280, DS18B20, Mikrofon) und Hauptplatine über das
   **RJ45-Kabel** verbinden.
4. Ggf. Umgebungsplatine (BH1750 / Helligkeit) anschließen. *(Einbauort noch zu ergänzen)*
5. Stromversorgung anschließen (siehe [Stromversorgung](stromversorgung.md)).

## Platzierung der Sensoren

- **DS18B20 (bis 3×):** über die Klemmen **Temp1/Temp2/Temp3** der Deckel-Platine, z. B.
  für Stock-, Innen- und Außentemperatur. *(Zuordnung noch zu ergänzen)*
- **BME280:** auf der Deckel-Platine für allgemeine Umweltwerte (Temperatur, Luftfeuchte,
  Luftdruck).
- **Mikrofon:** zur Erfassung von Stockgeräuschen.
- **BH1750 (Helligkeit):** auf der Umgebungsplatine.

## Wetterschutz, Diebstahl und Verschmutzung

> Dies ist eine häufige Frage aus der Imkerschaft und wird hier bewusst klargestellt.

- Die Platinen sind **nicht** mit zusätzlichen Maßnahmen gegen **Diebstahl** oder
  **Verschmutzung** gesichert.
- Der Schutz erfolgt ausschließlich über das **Außengehäuse**, in dem die Elektronik sitzt.
  Das Gehäuse schützt die Platinen vor **Witterung** (Regen, Staub).
- Es gibt **keinen eingebauten Diebstahlschutz** (z. B. Alarm oder GPS-Tracking).
  Wer das möchte, muss es zusätzlich selbst vorsehen. *(optional, nicht Teil des Designs)*

### Empfehlungen für den Außeneinsatz

- Gehäuse so montieren, dass **kein Spritz-/Stauwasser** eindringt; Kabeldurchführungen
  nach unten führen und abdichten.
- RJ45-Verbindung und Klemmen vor Feuchtigkeit schützen.
- Regelmäßig auf **Verschmutzung** (z. B. an Sensoren, Solarmodul) und festen Sitz prüfen.

> **Noch zu ergänzen:** empfohlener Gehäusetyp / Schutzart (IP-Klasse), Montagematerial,
> Hinweise zur Abdichtung der Kabeldurchführungen.

## Inbetriebnahme (Kurzüberblick)

1. Platinen bestücken (siehe [Fertigung](fertigung.md)) – **den ESP32 noch NICHT einsetzen**.
2. Hauptplatine **das erste Mal bestromen** und die Netzteil-Spannungen einstellen:
   **Batteriespannung 14,3 V** (nur bei Solar) und **5-V-Versorgung genau 5,0 V**, danach
   die **5-V-Lötbrücke schließen** (siehe
   [Stromversorgung → Erstes Bestromen und Spannungen einstellen](stromversorgung.md#erstes-bestromen-und-spannungen-einstellen)).
3. Erst **danach den ESP32 aufstecken** und am Messpunkt **3,3 V** prüfen (Kontrolle, dass
   der Controller korrekt versorgt wird).
4. ESP32 **flashen** – per **ESP Web Flasher** gemäß Anleitung im GitHub-Repo (siehe
   [Software](software.md#erstes-flashen-esp-web-flasher)).
5. Mit dem WLAN-Hotspot **`smartebeute`** (ohne Passwort) verbinden und die Waage
   **einrichten** (WLAN + ThingSpeak, siehe
   [Software → Erstkonfiguration](software.md#erstkonfiguration-über-den-hotspot)).
6. Platinen verbinden und montieren: Deckel-Platine und Umgebungsplatine über das
   **RJ45-Kabel** anschließen, Wägezelle (**4 Kabel**) an **Waage1/Waage2** anschließen
   (Belegung durch Ausprobieren, siehe
   [Software → Wägezelle anschließen](software.md#wägezelle-anschließen-4-kabel)).
7. Waage **kalibrieren** (0 kg / 10 kg, siehe
   [Software → Kalibrierung](software.md#kalibrierung-der-waage)).
8. Datenübertragung (WLAN an ThingSpeak, LoRa) prüfen.
9. Erste Messwerte kontrollieren.

## Wartung

- Akku-/Ladezustand im Blick behalten (besonders bei reinem Solarbetrieb im Winter).
- Sensoren und Solarmodul sauber halten.
- Nach Eingriffen am Stock prüfen, ob die Waage noch korrekt misst (ggf. neu kalibrieren).

> **Noch zu ergänzen:** Wartungsintervalle und Fehlersuche (Troubleshooting) für typische
> Probleme (keine Verbindung, unplausible Gewichtswerte, Sensor nicht erkannt).
