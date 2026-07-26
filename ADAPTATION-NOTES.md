# TruMinus für den Smartavan Heater Controller (Truma Combi Diesel)

Angepasste Version von [olivluca/TruMinus](https://github.com/olivluca/TruMinus) für dein Setup.

## Was wurde geändert

1. **Neues Board-Preset `SMARTAVAN`** in `src/main.cpp`:
   - UART: TX=GPIO16, RX=GPIO17 (identisch zu deiner bisherigen ESPHome-Config)
   - LED: GPIO2 (Standard-Onboard-LED der meisten WROOM32-Boards — prüfe das an
     deiner Hardware, ggf. GPIO anpassen oder LED-Code auskommentieren)
2. **`platformio.ini`**:
   - `board = esp32dev` (passend zum ESP32-WROOM-32D)
   - `-DSMARTAVAN` statt `-DGOOUUUC3` als Build-Flag
   - `-DCOMBIGAS` bleibt deaktiviert → du nutzt den Diesel-Zweig, der laut Projekt
     der ausgereifte/getestete Pfad ist (nicht der experimentelle Gas-Zweig)
3. **`src/globals.hpp`**: Home-Assistant-Discovery-Name auf generisches "Truma
   Heater" / "Combi D/DE (Diesel)" gesetzt — passe das bei Bedarf an.
4. **`src/wifi_config.h.example`**: Vorlage für WLAN/MQTT-Zugangsdaten.

## Was NICHT verändert wurde (und auch nicht nötig ist)

Die LIN-Bus-Bibliothek (`mestrode/Lin-Interface-Library`) erzeugt das Break-Signal
für jeden Frame selbst per UART-Neustart (`SERIAL_8N1`) — das ist eine andere,
aber gültige Implementierung der LIN-Frame-Einleitung als die 2-Stopbit-Methode,
die ESPHome/Fabian-Schmidts Komponente nutzt. Du musst hier nichts an Stopbits
etc. angleichen, die Bibliothek regelt das intern.

## Vor dem Bauen

1. `src/wifi_config.h.example` nach `src/wifi_config.h` kopieren und mit deinen
   echten WLAN- und MQTT-Zugangsdaten füllen.
2. Prüfen, ob GPIO2 auf dem Smartavan-Board wirklich frei/als LED nutzbar ist
   (Schaltplan/Silkscreen checken). Falls nicht: `#define LED` im `SMARTAVAN`-Block
   in `src/main.cpp` anpassen.
3. Physischer Hardware-Hinweis (wie bisher): RJ-12 und JST-Power-Kabel des CP Plus
   an die IN-Ports des Smartavan-Boards, OUT-Ports zum CP Plus — genau wie bei
   deiner bisherigen ESPHome-Installation, an der Verkabelung ändert sich nichts.

## Bauen & Flashen

```bash
# einmalig
pip install platformio --break-system-packages   # oder platformio.org/install

cd TruMinus-smartavan
pio run -e truminus                # kompilieren
pio run -e truminus -t uploadfs    # Webserver-Dateien (data/) hochladen, falls -DWEBSERVER aktiv
pio run -e truminus -t upload      # Firmware flashen (USB)
# danach OTA über truminus.local möglich (Env truminus_ota)
```

## MQTT-Topics (unverändert von TruMinus)

- Werte: `truma/status/room_temp`, `truma/status/water_temp`, `truma/status/error`, …
- Sollwerte: `truma/set/temp`, `truma/set/heating`, `truma/set/boiler`, `truma/set/fan`, …
- Home Assistant Discovery läuft automatisch unter `homeassistant/...` (dank
  `-DAUTODISCOVERY`), sobald das Gerät sich mit dem MQTT-Broker verbindet.

## Bekannte Einschränkung

Getestet ist dieser Fork bisher offiziell nur an einer Truma Combi D (eckige
Blende). Da du Diesel (D/DE) fährst, bist du im besser getesteten Zweig — trotzdem
beim ersten Testlauf die Heizung im Blick behalten (siehe Haftungsausschluss im
Original-Repo).
