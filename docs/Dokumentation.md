# Zisternenfüllstand – Bedienungsanleitung

**Version 2.6 | Stand: Mai 2026**

---

## Was macht dieses Gerät?

Das Gerät misst den Wasserstand in Ihrer Zisterne automatisch alle 2 Sekunden und zeigt das Ergebnis als:
- **Prozentwert** (z. B. „75 % voll")
- **Liter** (z. B. „1.500 Liter")

Die Messung erfolgt berührungslos mit einem Ultraschallsensor – ähnlich wie ein Einparksensor beim Auto.

Sie können das Gerät über jeden Webbrowser (Smartphone, Tablet, PC) bedienen und konfigurieren.

---

## Technische Übersicht (für Interessierte)

| Komponente | Details |
|---|---|
| Mikrocontroller | ESP32 |
| Sensor | Ultraschall HC-SR04 |
| Trigger-Pin | GPIO 16 |
| Echo-Pin | GPIO 4 (über Spannungsteiler) |
| Analogausgang | GPIO 25 (DAC, 0–3,3 V) |
| WLAN | 2,4 GHz, AP + STA gleichzeitig |
| Web-Oberfläche | Port 80, mDNS: `zisterne.local` |

---

## Inbetriebnahme – Schritt für Schritt

### Schritt 1: Gerät einschalten

Schließen Sie das Gerät an die Stromversorgung an (5 V USB oder entsprechende Spannungsversorgung). Die LED leuchtet auf bzw. der Serielle Monitor zeigt Aktivität – das Gerät startet.

**Wichtig:** Nach dem Einschalten öffnet das Gerät automatisch für **10 Minuten** einen eigenen WLAN-Zugangspunkt (Access Point). In dieser Zeit können Sie das Gerät immer konfigurieren, auch wenn noch kein Heim-WLAN eingerichtet ist.

---

### Schritt 2: Mit dem Gerät verbinden

1. Gehen Sie auf Ihrem Smartphone oder Computer in die **WLAN-Einstellungen**
2. Wählen Sie das Netzwerk **`CONFIG_ZISTERNE_AP`** aus
3. Passwort: **`12345678`**
4. Öffnen Sie einen Webbrowser und rufen Sie auf: **`http://192.168.4.1`**

Sie sehen jetzt die Startseite des Geräts.

---

### Schritt 3: Heim-WLAN einrichten (empfohlen)

Damit das Gerät dauerhaft in Ihrem Heimnetzwerk erreichbar ist:

1. Klicken Sie auf **„Konfiguration"**
2. Im Abschnitt **„Heim-WLAN"** geben Sie ein:
   - **SSID**: Name Ihres WLAN-Netzwerks (z. B. `MeinHeimnetz`)
   - **Passwort**: Ihr WLAN-Passwort
3. Klicken Sie **„Speichern & Verbinden"**

Das Gerät verbindet sich nun mit Ihrem Heimnetzwerk. Nach erfolgreicher Verbindung können Sie das Gerät über **`http://zisterne.local`** erreichen – von jedem Gerät im gleichen WLAN.

> **Tipp:** Notieren Sie sich die IP-Adresse, die im Footer der Webseite angezeigt wird (z. B. `192.168.1.42`). Diese können Sie alternativ zu `zisterne.local` verwenden, falls mDNS nicht funktioniert.

---

### Schritt 4: Sensor kalibrieren

Damit das Gerät den Füllstand korrekt berechnet, muss es wissen, wie weit der Sensor von der leeren bzw. vollen Zisterne entfernt ist.

Gehen Sie zu **Konfiguration → Kalibrierung**.

#### Option A: Automatisch kalibrieren (einfach)

1. **Leerer Zustand:** Zisterne muss leer (oder möglichst leer) sein → Klicken Sie **„Setze LEER = aktuelle Distanz"**
2. **Voller Zustand:** Zisterne muss voll (oder möglichst voll) sein → Klicken Sie **„Setze VOLL = aktuelle Distanz"**

Das Gerät misst jetzt automatisch und speichert die Werte.

#### Option B: Manuell kalibrieren (wenn Zisterne nicht leer/voll gemacht werden kann)

Messen Sie die Distanz vom Sensor bis zum Boden der Zisterne (= leer) und bis zur maximalen Wasserhöhe (= voll) mit einem Maßband:

1. **Leer (cm):** Distanz vom Sensor bis zum Boden (z. B. `320`)
2. **Voll (cm):** Distanz vom Sensor bis zum maximalen Wasserstand (z. B. `30`)
3. **Durchmesser (cm):** Innendurchmesser der Zisterne (für die Volumenberechnung, z. B. `150`)
4. Klicken Sie **„Speichern"**

> **Hinweis:** Der Leer-Wert ist immer größer als der Voll-Wert, da der Sensor bei leerer Zisterne weiter entfernt vom Wasser ist.

Nach der Kalibrierung zeigt die Startseite Füllstand in % und Volumen in Litern an.

---

### Schritt 5: Optionale Funktionen aktivieren

Alle optionalen Funktionen finden Sie unter **Konfiguration**.

#### API / Datenausgabe (für Smart-Home-Integration)

Aktivieren Sie **„Publishing"**, um den Füllstand über eine JSON-Schnittstelle abrufbar zu machen:

```
http://zisterne.local/api/status
```

Die Antwort sieht so aus:
```json
{
  "distance_cm": 150.5,
  "percent": 75.0,
  "analog_output": true,
  "volumen": 1250.30,
  "timestamp_ms": 123456789
}
```

Diese Schnittstelle können Sie in Home Assistant, ioBroker, Loxone oder ähnliche Systeme einbinden.

#### Analogausgang (0–3,3 V an GPIO 25)

Aktivieren Sie **„DAC-Ausgabe"**, um den Füllstand als analoge Spannung am Pin GPIO 25 auszugeben:
- **0 V** = Zisterne leer (0 %)
- **3,3 V** = Zisterne voll (100 %)

Dieser Ausgang kann z. B. an einen Analogeingang einer Steuerung (SPS, Loxone etc.) angeschlossen werden.

---

## Firmware aktualisieren

Sie können das Gerät einfach über den Browser aktualisieren – kein USB-Kabel nötig.

### Via Web-Browser (empfohlen)

Die Update-Seite ist passwortgeschützt:

| Feld | Wert |
|---|---|
| Benutzername | `admin` |
| Passwort | `zisternenfuellstand` |

1. Laden Sie die neue Firmware-Datei (`firmware.bin`) von der [GitHub-Releases-Seite](https://github.com/laussma/zisternenfuellstand/releases/latest) herunter
2. Öffnen Sie **`http://zisterne.local/update`** im Browser
3. Geben Sie Benutzername und Passwort ein
4. Klicken Sie auf **„Datei auswählen"** und wählen Sie die `firmware.bin`
5. Klicken Sie **„Hochladen & Flashen"**
6. Das Gerät startet automatisch neu – fertig!

### Automatisches Update (empfohlen)

Aktivieren Sie unter **Konfiguration → Firmware → Auto-Update**, damit das Gerät stündlich selbst auf neue Versionen prüft und sich automatisch aktualisiert. Keine weiteren Schritte nötig.

### Via Arduino IDE (für Entwickler)

Wenn das Gerät im gleichen WLAN ist, erscheint es in der Arduino IDE unter **Werkzeuge → Port** als Netzwerkport (`zisterne`). Als Passwort `zisternenfuellstand` eingeben.

---

## Statusseite – Was bedeutet was?

| Anzeige | Bedeutung |
|---|---|
| Distanz: `--` | Sensor liefert kein Signal (Hindernis, falsche Ausrichtung) |
| Füllstand: `--` | Kalibrierung fehlt (Schritt 4 ausführen) |
| Volumen: `--` | Durchmesser nicht konfiguriert oder Kalibrierung fehlt |
| Füllstand: `0 %` | Zisterne leer (oder Sensor-Fehlmessung) |
| Füllstand: `100 %` | Zisterne voll |

---

## Fehlerbehebung

| Problem | Lösung |
|---|---|
| Webseite nicht erreichbar | Gerät neu starten. Mit `CONFIG_ZISTERNE_AP` verbinden und `192.168.4.1` aufrufen. |
| `zisterne.local` funktioniert nicht | IP-Adresse aus dem Footer direkt eingeben. |
| Füllstand springt stark | Sensor nicht fest montiert oder Wasser stark bewegt → Messung wird gemittelt, kurz warten. |
| WLAN-Verbindung schlägt fehl | SSID und Passwort prüfen. Das Gerät versucht alle 2 Minuten neu zu verbinden. |
| Nach Update kein Zugriff | Gerät neu starten (Strom kurz trennen). |
| Serieller Monitor zeigt Fehler | USB-Verbindung mit Arduino IDE, Baudrate `115200` für Details. |

---

## WLAN-Verhalten im Überblick

```
Einschalten
    │
    ├─► Access Point "CONFIG_ZISTERNE_AP" startet (immer, 10 Minuten)
    │       └─► Zugriff via 192.168.4.1
    │
    ├─► Verbindung zum Heim-WLAN wird versucht (falls konfiguriert)
    │       └─► Erfolg: Gerät erreichbar via zisterne.local oder IP
    │
    └─► Nach 10 Minuten: AP schaltet sich ab wenn Heim-WLAN verbunden
            Kein Heim-WLAN: AP bleibt aktiv als Fallback
```

---

## Technische Details (für Entwickler)

### Webserver-Endpunkte

| URL | Methode | Funktion |
|---|---|---|
| `/` | GET | Statusseite (Distanz, Füllstand, Volumen) |
| `/config` | GET | Konfigurationsseite |
| `/api/status` | GET | JSON-Daten (nur wenn Publishing aktiviert) |
| `/measure` | GET | Sofortmessung |
| `/update` | GET | Firmware-Update-Seite |
| `/do_update` | POST | Firmware-Upload (multipart) |
| `/wifi` | POST | WLAN-Zugangsdaten speichern |
| `/sta` | POST | WLAN-Verbindung an/aus |
| `/set_leer` | POST | Leer-Wert auf aktuelle Messung setzen |
| `/set_voll` | POST | Voll-Wert auf aktuelle Messung setzen |
| `/set_manual` | POST | Kalibrierung manuell eingeben |
| `/publish` | POST | Publishing an/aus |
| `/analog` | POST | DAC-Ausgang an/aus |

### Persistente Einstellungen

Alle Einstellungen werden im Flash-Speicher des ESP32 gespeichert (NVS) und bleiben nach Neustart erhalten:

- WLAN-SSID und Passwort
- Kalibrierwerte (Leer-Distanz, Voll-Distanz, Durchmesser)
- Publishing aktiviert/deaktiviert
- Analogausgang aktiviert/deaktiviert

### Volumenberechnung

Das Gerät berechnet das Volumen einer **zylindrischen** Zisterne:

```
Füllhöhe [dm] = (Leer-Distanz - aktuelle Distanz) / 10
Volumen [Liter] = π × (Durchmesser/2 in dm)² × Füllhöhe
```

Für nicht-zylindrische Behälter ist der Prozentwert genauer als die Literangabe.

---

**Autor:** Matthias Lauß  
**Lizenz:** MIT
