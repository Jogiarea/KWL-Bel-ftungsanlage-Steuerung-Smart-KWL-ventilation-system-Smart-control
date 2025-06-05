# KWL-Belüftungsanlage-Steuerung-Smart-KWL-ventilation-system-Smart-control
Steuerung für kontrolierte Wohnraumbelüftung wie z.B. Maico (ebmpapst Lüfter) / Control unit for controlled domestic ventilation such as Maico (ebm-papst fan)

Hallo zusammen,

Hier möchte ich mit euch mein Projekt zur Smarten Wohnraumbelüftung KWL teilen.

Synergie:
Meine KWL von Maico ist leider nicht smart und Homeassistantfähig, domit habe ich beschlossen, diese Smart zu machen, um mit Homeassistant automatisieren zu können.
Das Smart-Modul von Maico würde mir nur eine App-Steuerung bringen, aber keine Anbindung an Homeassistant.
Da aber dann im Wohnzimmer die KWL Steuerung nicht mehr funktioniert und der Platz an der Wand nicht verschwendet werden soll, kommt noch eine zusätzliche Touchscreen-Steuerung zum Einsatz

Technische Daten:
 - Maico WR300 (Bestehende Anlage)
   - 2x ebm-papst G3G140... 0-10V oder PWM
 - ESP32 Node MCU ESP-WROOM-32 USB-Serial Chipsatz: CP2102 Pins: 38 Pin Version
   - Rohleiterplatte DIY Board 7x9
   - Widerstände 2x 100 Ohm
   - Optokoppler PC817 EL817 817 817C FL817C PS817C DIP4 DIP-4 DIP
   - 5x Printklemmen 2Pol Rastmaß 5,08
 - ESP32E28T CYD (Cheep Yellow Display)
 - 5V Netzteil > 2A
 
 
Links zu den Produkten, teilweise Affiliate-Links:
https://amzn.to/4kSvSnc
https://www.ebay.de/itm/374477253987
https://www.ebay.de/itm/375340795510?var=644317491447
https://www.ebay.de/itm/354540178989?var=623924135479
https://amzn.to/45FJNbB
https://amzn.to/45bLyxi
 

Die Lüfter von ebm-Papst benötigen eine 0-10V oder 10V PWM Signal, weshalb diese nicht direkt an den PWM-Ausgang eines ESP32-Node-MCU (3,3V PWM) angeschlossen werden können.
![ebm-papst Anschlussbild](https://github.com/user-attachments/assets/21883e3a-3bce-4703-be7b-128004538c61)![ebm-papst Anschlussbild](https://github.com/user-attachments/assets/aa29091a-e23f-47c4-ab9a-59046770c762)


Erster Schritt:

Etwas löten, verkabeln und brücken, schon ist die Hardware der Steuerung fertig.
Bei mir waren von 10St. Optokoppler 4 St. defekt, dann funktioniert das nicht.
![Lüftersteuerung ESP32 KWL](https://github.com/user-attachments/assets/d0cae38e-e5c0-4ffe-9ef6-3874ca9e1e09)
![PC817 Pinout](https://github.com/user-attachments/assets/2e70fc44-377e-40f6-bfc0-1fe1b94c2172)

Dann die Lüfter anschließen
![20250524_1459172](https://github.com/user-attachments/assets/86fcee89-27a2-4976-8496-c7d4d7d302a8)
![20250524_1459242](https://github.com/user-attachments/assets/62374509-f97f-4f96-a347-05905c491a12)
![20250524_1459282](https://github.com/user-attachments/assets/9ecbe014-321c-4d92-9440-f0c5d22d0db0)

Zweiter Schritt:

die Belüftungsanlage.yaml auf den ESP mittels ESP-Home hochladen, bitte nicht vergessen, auf eure Bedürfnisse anzupassen (siehe Bemerkungen in der .yaml)

Jetzt solltet ihr Zugriff auf die Steuerung über die definierte IP-Adresse haben und die Lüfter schon steuern können.
Ebenso sollte die Drehzahl jedes Lüfters angezeigt werden.

(Je nach Lüftungsanlage kann es sein, dass die Drehzahl der Lüfter unterschiedlich ist, dies kann dann daran liegen, dass die Lüfter Volumen-konstant sind und je nach Druck automatisch nachregeln, z.B. wenn in der Zuluft ein zusätzlicher Feinstaubfilter verbaut ist, in der Abluft aber nicht.)

Jetzt könnt ihr auch den ESP in Homeassistant mit der ESP-Home Integration in Homeassistant einbinden.

Dritter Schritt:
Automatisierung mit Homeassistant:

Im Anhang habe ich euch meine Automatisierung als yaml angehängt.
Ich habe einen Helfer mit vier Schaltzuständen erstellt  Ein / Aus / Manuel / Sommer (keine yaml vorhanden)

Dann eine Automatisierung zur Steuerung der Helfer erstellt: "Wohnraumlüftung Manuell"
Damit wird über den Helfer die jeweilige Funktion eingestellt

Und dann noch die Automatisierung für die Automatisierte Regelung "Wohnraumlüftung Steuerung"


Bei mir ist das dann so geregelt:
Ein --> Wohnraumlüftungssteuerung Automatik, regelt automatisch nach dem höchsten Wert meiner beiden CO₂-Sensoren die Drehzahl, außer die Außenluft ist wärmer als die Innenluft, dann aus, oder die Luftfeuchte im Bad ist > 70% dann Lüfterdrehzahl auf 30%
Aus --> Aus
Manuell --> Stellt beide Lüfter auf 30%, diese können dann Manuell nachgeregelt werden.
Sommer --> gleich wie bei ein, außer dass der Zuluftlüfter aus beleibt.

Die 30% Lüfter-PWM sind ungefähr der Party-Modus, also der alte Turbomodus meiner KWL.
Um herauszufinden, wie eure alte Steuerung die Drehzahl geregelt hat, kann man einfach die Leistungsaufnahme, z.B. mit einer Strommesssteckdose, aufnehmen und dann mit der neuen Steuerung sich an die Leistungsdaten herantasten.


Vierter Schritt: (nice to have)

Da die alte Steuereinheit im Wohnzimmer überflüssig ist und dort sonst ein Loch aus der Wand klaffen würde, habe ich beschlossen, dort eine Steuerung hinzubasteln.
Die Steuerung kann zum Steuern der KWL verwendet werden, aber auch für alles andere Steuerbare in Homeassistant.

Bei diesem Projekt habe ich mich weitgehend an dieses hier angehängt und an meine bedürfnisse angepasst.
https://aguacatec.es/integrar-la-cheap-yellow-display-en-ha/
Vielen Dank an den Ersteller.


Ebenso auch danke an 
https://github.com/witnessmenow/ESP32-Cheap-Yellow-Display
und seinen Discord-Kanal

![20250602_112222](https://github.com/user-attachments/assets/27447274-c3e8-4c64-9d62-fa83b3955dcd)

Die ESP Home yaml findet ihr wieder im Anhang und müsst diese noch an euch anpassen.

Zu beachten ist, es gibt mittlerweile mehrere Versionen des CYD (Cheap-Yellow-Display) hier kann auch sein, dass ihr noch andere Treiber benötigt und ggf. die Displaydrehung sowie den Touch anders konfigurieren müsst.

Das Ganze habe ich mit 5V, mit der vorhandenen Leitung der Alten KWL Steuerung von dem gleichen Netzteil wie bei Schritt eins mit Strom versorgt. Der CYD hat einen 5V auf 3,3V Spannungsregler verbaut. 




------------------------------------------------------------------------
Hello everyone,

I would like to share my project for smart domestic ventilation KWL with you here.

Synergy:
Unfortunately, my KWL from Maico is not smart and Homeassistant-capable, so I decided to make it smart so that I can automate it with Homeassistant.
The smart module from Maico would only give me app control, but no connection to Homeassistant.
However, as the KWL control system would no longer work in the living room and the space on the wall should not be wasted, an additional touchscreen control will be used

Technical data:
 - Maico WR300 (existing system)
   - 2x ebm-papst G3G140... 0-10V or PWM
 - ESP32 Node MCU ESP-WROOM-32 USB-Serial Chipset: CP2102 Pins: 38 Pin Version
   - Raw PCB DIY Board 7x9
   - Resistors 2x 100 Ohm
   - Optocoupler PC817 EL817 817 817C FL817C PS817C DIP4 DIP-4 DIP
   - 5x PCB terminals 2-pole snap-in dimension 5.08
 - ESP32E28T CYD (Cheep Yellow Display)
 - 5V power supply > 2A
 
 
Links to the products, some of them affiliate links:
https://amzn.to/4kSvSnc
https://www.ebay.de/itm/374477253987
https://www.ebay.de/itm/375340795510?var=644317491447
https://www.ebay.de/itm/354540178989?var=623924135479
https://amzn.to/45FJNbB
https://amzn.to/45bLyxi
 

The fans from ebm-Papst require a 0-10V or 10V PWM signal, which is why they cannot be connected directly to the PWM output of an ESP32 node MCU (3.3V PWM).
![ebm-papst Anschlussbild](https://github.com/user-attachments/assets/21883e3a-3bce-4703-be7b-128004538c61)![ebm-papst Anschlussbild](https://github.com/user-attachments/assets/aa29091a-e23f-47c4-ab9a-59046770c762)


First step:

Solder, wire and bridge something, and the control hardware is ready.
In my case, 4 of the 10 optocouplers were defective, so it doesn't work.
![Lüftersteuerung ESP32 KWL](https://github.com/user-attachments/assets/d0cae38e-e5c0-4ffe-9ef6-3874ca9e1e09)
![PC817 Pinout](https://github.com/user-attachments/assets/2e70fc44-377e-40f6-bfc0-1fe1b94c2172)

Then connect the fans:
![20250524_1459172](https://github.com/user-attachments/assets/86fcee89-27a2-4976-8496-c7d4d7d302a8)
![20250524_1459242](https://github.com/user-attachments/assets/62374509-f97f-4f96-a347-05905c491a12)
![20250524_1459282](https://github.com/user-attachments/assets/9ecbe014-321c-4d92-9440-f0c5d22d0db0)


Second step:

Upload the ventilation system.yaml to the ESP using ESP-Home, please do not forget to adapt it to your needs (see comments in the .yaml).

You should now have access to the controller via the defined IP address and be able to control the fans.
The speed of each fan should also be displayed.

(Depending on the ventilation system, the speed of the fans may be different; this may be due to the fact that the fans are volume-constant and automatically readjust depending on the pressure, e.g. if an additional fine dust filter is installed in the supply air but not in the exhaust air).

Now you can also integrate the ESP in Homeassistant with the ESP-Home integration in Homeassistant.

Third step:
Automation with Homeassistant:

In the attachment I have attached my automation as yaml for you.
I have created a helper with four switching states On / Off / Manual / Summer (no yaml available)

Then created an automation to control the helpers: "Manual living room ventilation"
This is used to set the respective function via the helper

And then the automation for the automated control "Living room ventilation control"

In my case, it's regulated like this:
On --> Automatic living room ventilation control, automatically regulates the speed according to the highest value of my two CO₂ sensors, unless the outside air is warmer than the inside air, then off, or the humidity in the bathroom is > 70% then fan speed to 30%
Off --> Off
Manual --> Sets both fans to 30%, these can then be manually readjusted.
Summer --> same as on, except that the supply air fan remains off.

The 30% fan PWM is approximately the party mode, i.e. the old turbo mode of my KWL.
To find out how your old control system regulated the speed, you can simply record the power consumption, e.g. with a current measuring socket, and then approach the performance data with the new control system.


Fourth step: (nice to have)

As the old control unit in the living room is superfluous and there would otherwise be a gaping hole in the wall, I decided to install a control unit there.
The control unit can be used to control the KWL, but also for everything else that can be controlled in Homeassistant.

For this project, I largely based it on this one and adapted it to my needs.
https://aguacatec.es/integrar-la-cheap-yellow-display-en-ha/
Many thanks to the creator.

Thanks also to 
https://github.com/witnessmenow/ESP32-Cheap-Yellow-Display
and his Discord channel
![20250602_112222](https://github.com/user-attachments/assets/27447274-c3e8-4c64-9d62-fa83b3955dcd)

The ESP Home yaml can be found in the appendix and must still be adapted to your needs.








![20250525_154514](https://github.com/user-attachments/assets/c5bc5f18-2cbb-49bd-915c-b8e568754cd4)


Please note that there are now several versions of the CYD (Cheap-Yellow-Display) here you may also need other drivers and may have to configure the display rotation and the touch differently.

I have powered the whole thing with 5V, using the existing cable of the old KWL controller from the same power supply unit as in step one. The CYD has a 5V to 3.3V voltage regulator built in. 
