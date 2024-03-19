# ha.vis.vitocal250
Visualisierung des Kältekreislaufs der Viessmann Wärmepumpe VitoCal 250 in Home Assistant

# Initiale Version

Ausgangspunkt war die existierende Visualisierung im HMI der Vitocal und dessen Umsetzung für ioBroker auf [https://github.com/MyHomeMyData/iob.vis.vitocal250]
<img width="1360" alt="image" src="https://github.com/jwende/ha.vis.vitocal250/assets/3383864/8d7b952a-55a8-4637-9fb6-94d157fae1c9">

## Voraussetzungen
Um die benötigten Daten aus der VitoCal auszulesen, ist zZ nur ein Weg bekannt: USB2CAN-Adapter an den externen CAN-Bus der VitoCal anschließen und per Python-Skript die benötigten Daten abfragen. Über das API-Interface von Viessmann kommt man nicht an (alle) diese Daten ran.

Foglendes wird benötigt:
- System mit HomeAssistant, z.B. auf einem Rapsberry Pi.
- Bei Verwendung von open3e zusätzlich: Linux System mit Python 3 und MQTT-CLient z.B. auf einem Rapsberry Pi.
- Installation des Adapters von [open3e](https://github.com/abnoname/open3e)
- USB2CAN-Adapter

## Einrichten der Visualisierung mit open3e
- Zunächst muss dafür gesorgt werden, dass die benötigten Daten regelmäßig per MQTT versendet werden. Das erledigt open3e mit folgendem Aufruf: `python3 Open3Eclient.py -c can0 -dev vcal -m <hostname>:1883:open3e/vitocal -mfstr {didNumber:04d}_{didName} -t 30 -r 268,269,271,274,284,318,320,321,322,324,325,355,381,389,391,396,1043,1769,1771,1772,1775,1776,2333,2334,2346,2351,2487,2488,2496,2735,3016`
- Damit werden die Daten alle 30 Sekunden per MQTT an den Broker auf <hostname> gesendet. Z.B. die Temperatur am Eingang des Kompressors wird dann als Nachricht an folgendes Topic vesendet: `open3e/vitocal/0321_CompressorInletTemperatureSensor`
- Es muss im HomeAssistant eine MQTT Instanz eingerichtet und funktionabel sein - entweder als Mosquitto Server oder als Client zu einem externen System.
- Es muss die Konfigurationsdatei (configuration.yaml) mit dem Inhalt von 'config\MQTT_vc250.json' ergänzt werden 
- Für die Visualisierung im Homeassistant müssen die Dateien aus diesem Repository unter `img/` nach `local/img/` hochgeladen werden, dazu verwendet man am besten den Samba Client.
- Nun erzeugt man ein Dashboard und importiert den Inhalt von `view/Kaeltekreis.json` per "copy & paste" im Raw-Konfiguarationseditor.
  ![image](https://github.com/jwende/ha.vis.vitocal250/assets/3383864/28912996-fd2b-416c-9bcb-3503508c0a00)

