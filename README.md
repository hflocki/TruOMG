# TruOMG

Hobby-/Bastelprojekt zur Truma-Combi-Heizungssteuerung über MQTT
auf einem Smartavan Heater Controller (ESP32-WROOM-32D).

**Hinweis zum Namen:** TruOMG ist kein Teil und keine Modifikation des offiziellen
[OpenMQTTGateway](https://github.com/1technophile/OpenMQTTGateway)-Projekts (OMG)
und steht in keiner Verbindung dazu. Der Name spielt nur auf die ursprüngliche
Idee an ("Truma auf MQTT-Gateway-Basis"), tatsächlich basiert die Firmware auf
der eigenständigen Codebasis von TruMinus (siehe Quellen unten). Ebenso keine
Verbindung zu oder Unterstützung durch die Truma Gerätetechnik GmbH & Co. KG.

## Was TruOMG macht

- Emuliert das Truma CP Plus Bedienteil auf dem LIN-Bus, liest/steuert eine
  Truma Combi Diesel (D/DE) über den Smartavan Heater Controller
- Veröffentlicht Status und nimmt Sollwerte über MQTT entgegen
  (`truma/status/...`, `truma/set/...`)
- Home-Assistant-Autodiscovery inklusive

## Hardware

- [Smartavan Heater Controller](https://shop.smartavan.com/products/smartavan-heater-controller)
  (ESP32-WROOM-32D), verkabelt wie gewohnt zwischen Truma CP Plus und Heizung
- UART: TX=GPIO16, RX=GPIO17

## Bauen & Flashen

```bash
cd TruOMG
cp src/wifi_config.h.example src/wifi_config.h   # eigene WLAN/MQTT-Daten eintragen
pio run -e truminus                # kompilieren
pio run -e truminus -t uploadfs    # Web-UI-Dateien hochladen (falls -DWEBSERVER aktiv)
pio run -e truminus -t upload      # Firmware flashen (USB)
```

Details zu Board-Preset, Build-Flags und Sonderfällen: siehe unten.

## Quellen / basiert auf

Dieses Projekt wäre ohne die Vorarbeit folgender Projekte nicht möglich gewesen:

- **[olivluca/TruMinus](https://github.com/olivluca/TruMinus)** — Basis-Codebasis
  (LIN-Bus-Kommunikation, MQTT-Anbindung, HA-Autodiscovery, Webinterface).
  TruOMG ist im Kern ein Hardware-Fork davon, angepasst auf den Smartavan
  Heater Controller.
- **[mestrode/Lin-Interface-Library](https://github.com/mestrode/Lin-Interface-Library)** —
  LIN-Bus-Low-Level-Protokoll (von TruMinus als Abhängigkeit genutzt)
- **[Fabian-Schmidt/esphome-truma_inetbox](https://github.com/Fabian-Schmidt/esphome-truma_inetbox)**
  und Forks ([havanti/esphome-truma](https://github.com/havanti/esphome-truma),
  [thomas70/esphome-truma_inetbox](https://github.com/thomas70/esphome-truma_inetbox)) —
  ESPHome-Referenzimplementierung, ursprünglich für dieses Setup verwendet
- **[danielfett/inetbox.py](https://github.com/danielfett/inetbox.py)**,
  **[muccc/WomoLIN](https://github.com/muccc/WomoLIN)**,
  **[mc0110/inetbox2mqtt](https://github.com/mc0110/inetbox2mqtt)** — ursprüngliche
  Protokoll-Reverse-Engineering-Arbeit, auf der die meisten Truma-LIN-Projekte
  aufbauen
- **[smartavan/smartavan-heater-controller](https://github.com/smartavan/smartavan-heater-controller)** —
  Referenzkonfiguration für die verwendete Hardware
- **[vincentgloss/trumanita](https://github.com/vincentgloss/trumanita)** — als
  alternativer Ansatz (Raspberry-Pi-basiert) mit evaluiert, nicht direkt verwendet

Die ursprüngliche TruMinus-README liegt zur Referenz unter
[`UPSTREAM-README-TruMinus.md`](UPSTREAM-README-TruMinus.md), die Smartavan-
Anpassungsdetails unter [`ADAPTATION-NOTES.md`](ADAPTATION-NOTES.md).

## Lizenz

Wie TruMinus: GPL-3.0 (siehe `LICENSE`).

## Haftungsausschluss

Dieses Projekt ist ein Hobbyprojekt ohne jede Gewährleistung. Nutzung auf
eigene Gefahr — insbesondere bei einer Anwendung, die eine Dieselheizung
steuert. Neue Firmware immer unter Aufsicht testen.
