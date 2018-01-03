<!--

author:   Georg Jäger

email:    gjaeger@ovgu.de

version:  1.0.0

language: de_DE

narrator:  Deutsch Female

-->

# Einführung

Willkommen zurück im eLearning-System *eLab*.

--{{1}}--
In der letzten Aufgabe habt ihr bereits zwei wichtige Sensortypen (Distanzsensoren, IMU) kennengelernt. Während ihr mit Hilfe der Distanzsensoren die Umgebung des Roboters wahrnehmen könnt, erlaubt euch die IMU Bewegungen des Roboters selber festzustellen. 

--{{2}}--
Neben der IMU, wird vor allem aber auch die Odometrie genutzt, um die Ausführung von Bewegungsbefehlen eines Roboters zu überwachen. Daher wird das erste Ziel dieser Aufgabe sein, die Odometrie unseres Roboters in Betrieb zu nehmen.

--{{3}}--
Nachdem wir dann alle relevanten Sensoren und Aktoren unseres Roboters auslesen bzw. ansteuern können, nutzen wir diese Möglichkeiten zur Umsetzung einer autonomen Bewegungsstrategie. 

--{{4}}--
Ziel wird es sein, sowohl eine Kollisionsvermeidung, als auch einen [Wall Follower](https://en.wikipedia.org/wiki/Maze_solving_algorithm) zu implementieren. Während die Kollisionsvermeidung sicherstellt, dass unser Roboter nicht frontal mit einem Objekt kollidiert, erlaubt die Wandverfolgung unserem Roboter sich (theoretisch) durch ein Labyrinth zu finden.


## Themen und Ziele

--{{1}}--
Das Ziel dieser Aufgabe ist einen *Wall Follower* zu implementieren. Da dieser von unserem Roboter verlangt, nicht nur seine Umgebung wahrzunehmen, sondern auch seine Bewegungen zu planen und korrekt auszuführen, muss zunächst ein weitere Sensor, die Odometrie, in Betrieb genommen werden. Da sie auf der Abhandlung von Interrupts basiert, bilden diese einen Schwerpunkt der Aufgabe.

--{{2}}--
Mit Hilfe der Odometrie wird es uns ermöglicht, zu überprüfen, ob unser Roboter auch unsere Steuerungskommandos so ausführt, wie wir sie geplant haben. Wie uns auffallen wird, ist das nicht immer der Fall, sodass wir eine weiter Komponente zur Motoransteuerung hinzufügen müssen: eine Regelung. 

--{{3}}--
Zwar gibt es eine Vielzahl von Möglichkeiten eine Regelung umzusetzen, weit verbreitet sind allerdings die PID-Regler. Daher werden wir uns auf diese beschränken.

--{{4}}--
Letztlich bieten uns die verschiedenen Komponenten unseres Roboters nun die Möglichkeit eine autonome Bewegungsstrategie zu implementieren. Daher bildet das Thema des *Motion Planning* den letzten Schwerpunkt dieser Aufgabe.


**Themen:**

* Interrupts
* Odometrie
* PID Regler
* Motion Planning


**Ziel(e):**

* Implementierung einer Interrupt-basierten Odometrie
* Geschwindigkeitsregelung der Motoren mit Hilfe von PID Reglern
* Implementierung einer Bewegungsstrategie zur Wandverfolgung.


## Weitere Informationen

--{{1}}--
Da zur korrekten Implementierung der Odometrie die Konfiguration und Nutzung von Interrupts benötigt wird, haben wir euch, neben der Vorlesung, weitere einführende Referenzen aufgelistet. 

--{{2}}--
Darüber hinaus könnt ihr Informationen sowohl zur Odometrie als auch zu Rotationsencoder finden. 

--{{3}}--
Wie schon erwähnt sollen die Daten der Odometrie zur Geschwindigkeitsregelung der Motoren genutzt werden. Daher haben wir auch einführende Artikel zu PID Reglern aufgelistet.

--{{4}}--
Letztlich sind die bisher besprochenen Komponenten unseres Roboters lediglich Voraussetzungen zur Umsetzung von autonomen Bewegungsstrategien und Verhaltensmustern. Da diese allerdings nicht in dieser Veranstaltung behandelt werden können, wollen wir euch zumindest Ansatzpunkte für weitere Recherchen geben. Zum einen bietet das (allgemeine) Gebiet der autonomen Roboter vielseitige Herausforderungen die zunehmend durch Algorithmen und Methodiken der künstlichen Intelligenz gelöst werden. Zum anderen bilden aber vor allem die Bereiche des *Simultaneous localization and mapping (SLAM)* und des *Motion Planning* konkrete Bereiche der Robotik, die grundlegend für die Autonomie von Robotern sind.

--{{5}}--
Wie immer findet ihr hier aber auch die Datenblätter der Komponenten unserer Roboterplattform.

**Interrupts:**

* [Mikrocontroller.net](https://www.mikrocontroller.net/articles/Interrupt)
* [Übersicht](https://en.wikipedia.org/wiki/Interrupt)

**Odometrie:**

* [Odometrie](https://en.wikipedia.org/wiki/Odometry)
* [Rotationsencoder](https://en.wikipedia.org/wiki/Rotary_encoder)

**PID-Controller:**

* [Einfache Einführung](https://www.csimn.com/CSI_pages/PIDforDummies.html)
* [Überblick](https://en.wikipedia.org/wiki/PID_controller)
* Einführende Erklärungen:

  !![PIDIntroduction](https://www.youtube.com/embed/UR0hOmjaHp0)<!--
    width: 560px;
    height: 315px;
  -->
  
**Robotik:**

* [Allgemeine Einführung in das Gebiet der autonomen Roboter](https://github.com/correll/Introduction-to-Autonomous-Robots/releases/download/v1.9/book.pdf) 
  
**Simultaneous localization and mapping (SLAM):**

* [Übersicht bei Wikipedia](https://en.wikipedia.org/wiki/Simultaneous_localization_and_mapping)
* [Vorlesung zu SLAM (YouTube)](https://www.youtube.com/watch?v=U6vr3iNrwRA&list=PLgnQpQtFTOGQrZ4O5QzbIHgl3b1JHimN_)
* Visualisierung von SLAM:

  !![SimplePresentationSLAM](https://www.youtube.com/embed/SeNLUW79_-c)<!--
    width: 560px;
    height: 315px;
  -->

**Motion Planning:**

* [Vorlesungsfolien](https://ipvs.informatik.uni-stuttgart.de/mlr/marc/teaching/14-Robotics/04-pathPlanning.pdf)
* Erklärungen zu Motion Planning in relation zu SLAM:

  !![Visualisierungsvideo](https://www.youtube.com/embed/qXZt-B7iUyw?list=PLKwqbLx4DLDGkT0Tc6yiqsjDmH395hgvF)<!--
    width: 560px;
    height: 315px;
  -->

**PKeS:**

* [Datenblatt IMU (MPU-9255)](https://stanford.edu/class/ee267/misc/MPU-9255-Datasheet.pdf)
* [Datenblatt IMU (MPU-9255) Register Map](https://stanford.edu/class/ee267/misc/MPU-9255-Register-Map.pdf)
* [Datenblatt infrarot Distanzsensor Sharp GP2Y0A41SK0F](http://sharp-world.com/products/device/lineup/data/pdf/datasheet/gp2y0a41sk_e.pdf)
* [Datenblatt infrarot Distanzsensor Sharp GP2Y0A02YK0F]( http://sharp-world.com/products/device/lineup/data/pdf/datasheet/gp2y0a02yk_e.pdf)
* [Datenblatt Motortreiber](http://www.st.com/content/ccc/resource/technical/document/datasheet/59/d0/ce/41/56/bb/4b/10/DM00034699.pdf/files/DM00034699.pdf/jcr:content/translations/en.DM00034699.pdf)
* [Schaltbelegungsplan](https://github.com/liaScript/PKeS0/blob/master/materials/robubot_stud.pdf?raw=true)
* [Arduinoview](https://github.com/fesselk/Arduinoview/blob/master/doc/Documetation.md)
* [Datenblatt des AVR ATmega32U4](http://www.atmel.com/Images/Atmel-7766-8-bit-AVR-ATmega16U4-32U4_Datasheet.pdf)
* [Interrupt Reference in der AVR Libc](http://www.atmel.com/webdoc/avrlibcreferencemanual/group__avr__interrupts.html)


# Aufgabe 4

--{{1}}--
Wie schon beschrieben, besteht die erste Teilaufgabe darin, die Odometrie des Roboters in Betrieb zu nehmen. Dazu müsst ihr vor allem die Möglichkeiten der *Pin Change Interrupts* nutzen.

--{{2}}--
Basierend auf den Daten könnt ihr dann eine Geschwindigkeitsregelung für die Motoren (links und rechts) implementieren. 

--{{3}}--
Nachdem wir dann die Umgebung unseres Roboters wahrnehmen können und darauf reagieren können, sollte das erste Ziel unserer Bewegungsstrategie sein, eine Kollisionsvermeidung zu implementieren. So soll verhindert werden, dass unser Roboter mit einem Objekt kollidiert.

--{{4}}--
Da die Kollisionsvermeidung allerdings lediglich reaktiv ist, wollen wir darüber hinaus auch eine aktive Bewegungsstrategie implementieren. In unserem Fall soll der Roboter eine Wandverfolgung durchführen.


**Hinweis:**

**Verwendet bei der Bearbeitung der Aufgabe keine Funktionen aus der Arduino-Bibliothek. Lediglich die Funktionen der `Serial`-Klasse zur Ansteuerung der seriellen Schnittstelle, die Funktionen `millis()` und `micros()` zur einfachen Zeitmessung, sowie die Funktionen aus `Wire.h` für die I^2^C Kommunikation können genutzt werden.**


**Teilaufgaben**

* *4.1:* Implementierung der Odometrie.
* *4.2:* PID-Regelung der Geschwindigkeit der Motoren.
* *4.3:* Kollisionsvermeidung auf Basis der IR-Distanzsensoren.
* *4.4:* Wandverfolgung.

Die Aufgabe ist bis zu der Woche vom **22.01. - 26.01.2018** vorzubereiten.

## Aufgabe 4.1

--{{1}}--
Das Ziel dieser Aufgabe ist es, die Odometrie an unserem Roboter in Betrieb zu nehmen. Dazu müsst ihr zunächst die entsprechenden Interrupts konfigurieren und eine Interruptroutine schreiben, um die Ticks der inkrementeller Rotationsencoder auszulesen.

--{{2}}--
Durch die Interrupts ist die Funktionalität der Odometrie bereits implementiert. Um ihre Daten auch im weiteren Programm nutzbar zu machen, benötigen wir noch ein entsprechendes Interface. Implementiert dazu die Funktion `odomTicks(struct OdomData& d)`, wie sie durch `Odometry.h` vorgegeben ist. Sie sollte die Anzahl von Ticks für beide Motoren zurückgeben. Bei der Implementierung dieser Funktion ist zu beachten, dass keine weiteren Interrupts das Auslesen der Register stören, da so inkonsistente Daten entstehen könnten.

--{{3}}--
Da *Ticks* ein hardwarenahes Maß ist, wollen wir zur weiteren Verwendung daraus in einem dritten Schritt die Geschwindigkeit und zurückgelegte Distanz berechnen. Implementiert dazu die in `Odometry.h` vorgegebenen Konvertierungsfunktionen `odomVelocity( int32_t dticks, float dtime)` und `odomDistance( int32_t dticks)`. Die Geschwindigkeit sollte in $\frac{m}{s}$ während die Distanz in $m$ zurückgegeben werden sollte.

**Hinweis:**

Der Durchmesser für ein Rad beträgt *65mm*. 
Die Odometrie jedes Rades besteht aus [inkrementellen Rotationsgebern](https://en.wikipedia.org/wiki/Rotary_encoder#Incremental_rotary_encoder). Bei jeder Radumdrehung durchlaufen diese 224,20 Perioden in 4 Phasen. Beachtet die verschiedenen Drehrichtungen der Räder!

**Ziel:**

Implementierung der Odometrie.

**Teilschritte:**

1. Konfiguration der Interrupts (`initOdom()`) und Implementierung der *Interrupt-Service-Rountine*.
2. Implementiert die Funktion `odomTicks(struct OdomData& d)`.
3. Implementiert die Funktionen `odomVelocity( int32_t dticks, float dtime)` und `odomDistance( int32_t dticks)`

## Aufgabe 4.2

--{{1}}--
Die Odometrie, die wir in der vorhergehenden Aufgabe implementiert haben, erlaubt es uns nun, die Geschwindigkeit der Motoren/Räder zu regeln. Dazu sollt ihr in dieser Teilaufgabe einen PID-Regler implementieren und parametrisieren.

--{{2}}--
Implementiert im ersten Schritt die Funktionalität eines PID-Reglers. Dies soll separat passieren, daher haben wir euch die leere `PID.h`-Datei vorgegeben.

--{{3}}--
In einem zweiten Schritt wollen wir nun die eigentliche Steuerung der Motoren durch den PID-Regler abstrahieren. Implementiert dazu die Funktionen der `MotorControl.h`. 

--{{4}}--
Beginnt mit den Funktionen `stopMotors()` und `startMotors()`. Wie schon in der Aufgabe 2, soll zunächst die Möglichkeit geschaffen werden, die Motoren jederzeit zu stoppen. Dies soll durch die entsprechende Funktion `stopMotors()` sichergestellt werden. Achtet darauf, dass ihr die Motoren mit Hilfe dieser Funktion zu jeder Zeit deaktivieren könnt!

--{{5}}--
Das Gegenstück zu `stopMotors()` bildet die Funktion `startMotors()` mit deren Hilfe ihr die Motoren freigeben und aktivieren können solltet.

--{{6}}--
Nun könnt ihr die Ansteuerung und Regelung der Motoren durch einen PID-Regler implementieren. Mit der Funktion `setVelocityMotors( float left, float right )` sollen die Zielgeschwindigkeiten für die Räder vorgegeben werden können. Da der PID-Regler nun regelmäßig entsprechende Steuerkommandos an die Motoren weitergeben muss damit diese Zielgeschwindigkeiten auch erreicht werden, soll während der `updateMotors()` Funktion die Berechnung der Steuersignale für die Motoren stattfinden und an die Motoren weitergeben werden.

--{{7}}-- 
Zur Schonung der Motoren sollt ihr folgende Regel beachten: Wenn Ist- und Soll-Geschwindigkeiten aller Motoren 0 sind und die Steuersignale für die Motoren zwischen -15 und 15 liegt, werden die Motoren deaktiviert. Weicht einer der genannten Prameter ab, sind die Motoren wieder zu aktivieren.

--{{8}}--
Letztlich müsst ihr noch die Parameter des PID-Reglers bestimmen. Dazu könnt ihr zum einen die [Ziegler-Nichols Methode](https://en.wikipedia.org/wiki/Ziegler%E2%80%93Nichols_method) verwenden, zum anderen könnt ihr versuchen eine Geradeausfahrt durch eine gute Parametrisierung zu erreichen.

**Ziel:**

Regelt die Drehgeschwindigkeit der Motoren mit Hilfe eines PID Regler.

**Teilschritte:**

1. Implementierung einen PID-Regler in der `PID.h`-Datei.
2. Implementierung der Interface-Funktionen `stopMotors()` und `startMotors()`.
3. Implementierung der Interface-Funktionen `setVelocityMotors( float left, float right )` und `updateMotors()`.
4. Parametrisierung des Reglers für beide Motoren.

## Aufgabe 4.3

--{{1}}--
Mit der Regelungsstrategie unserer Motoren können wir nun eine autonome Bewegungsstrategie implementieren. Da dies bedeutet, dass unser Roboter autonom die Geschwindigkeitsvorgaben für die Motoren bestimmen wird, müssen wir dabei auch die Sicherheit des Systems beachten. 

--{{2}}--
Allgemein bedeutet dies, dass wir zum Beispiel Kollisionen unseres Roboters mit Objekten in der Umgebung vermeiden sollten. Da uns allerdings nur Distanzsensoren an der Front unseres Roboters zur Verfügung stehen, können wir zunächst auch nur frontale Kollisionen vermeiden.

--{{3}}--
Daher ist das Ziel dieser Teilaufgabe, eine Kollisionsvermeidung auf Basis der IR-Distanzsensoren zu implementieren. 


**Ziel:**

Nutzt die IR-Distanzsensoren um frontale Kollisionen zu vermeiden.

**Teilschritte:**

1. Implementiert eine Schutzfunktion zur Vermeidung von frontalen Kollisionen.
2. Legt empirisch einen minimalen Distanzwert für die Kollisionsvermeidung fest.

## Aufgabe 4.4

--{{1}}--
Nachdem unsere Sensoren und Aktoren, sowie eine einfache Kollisionsvermeidung einsatzbereit sind, können wir diese Komponenten nutzen um eine weitere Bewegungsstrategie zu implementieren: eine Wandverfolgung.

--{{2}}--
Implementiert zunächst einen Button in Arduinoview um zwischen dem manuellen und dem autonomen Modus umschalten zu können. Beachtet, dass vor allem der *Stop* Button in jedem Fall zum Stoppen des Roboters genutzt werden können sollte.

--{{3}}--
Ein Problem beim Lösen dieser Aufgabe ist, dass unsere Distanzsensoren lediglich an der Front unseres Roboters montiert sind. Da wir dementsprechend nicht direkt unsere aktuelle, seitliche Distanz zur Wand messen können, müsst ihr den Roboter von Zeit zu Zeit erneut orientieren. 

--{{4}}--
Eine Möglichkeit zur Umsetzung wäre: Ihr könnt die IMU nutzen und den Roboter auf der Stelle drehen lassen. So könnt ihr die naheliegendste Wand und eure Distanz zu dieser detektieren. 

--{{5}}--
Wenn ihr die Distanz zu naheliegendsten Wand kennt, könnt ihr abhängig davon eure Bewegung planen. Das heißt in welche Richtung ihr euch bewegen wollt (Soll die Wand auf der linken oder rechten Seite des Roboters sein?) und wie ihr möglichst parallel zur Wand fahren könnt. Überlegt außerdem, wann es nötig sein wird den Roboter erneut zu orientieren.

--{{6}}--
Durch wiederholtes Planen der Bewegungsstrategie und Re-Orientieren des Roboters sollte es euch möglich sein, der Wand in einer Distanz zwischen *10cm* und *20cm* zu folgen.

--{{7}}--
Während der Implementierung werdet ihr mehrere Parameter, die das Verhalten eures Roboters beeinflussen werden, bestimmen. Legt diese in Abhängigkeit zur Ungenauigkeit eurer Sensorik und Aktorik fest. Dies wird voraussichtlich ein empirisches Vorgehen erfordern.

--{{8}}--
Das hier vorgestellte Vorgehen zur Wandverfolgung ist nur eine Möglichkeit. Wir sind gespannt, welche Lösungen ihr findet!

**Ziel:**

Implementiert eine Wandverfolgung.

**Teilschritte:**

1. Implementiert einen Button in Arduinoview zum Umschalten zwischen manuellen und autonomen Modus
2. Implementiert eine initiale Lokalisierung: "Wo ist die nächste Wand?"
3. Plant eine Bewegung zur Wand, sodass ihr in einer Distanz von *10-20cm* neben der Wand steht.
4. Beginnt die Wandverfolgung. Eine Möglichkeit wäre:

   1. Plant eine Teilstrecke entlang der Wand und berechnet die entsprechenden Steuerkommandos
   2. Re-orientiert den Roboter um eine weiter Teilstrecke zu planen.
   3. Wiederholt Schritte 1 und 2 um euch entlang der Wand zu bewegen.

# Quizze

--{{1}}--
Wie auch in der letzten Aufgabe haben wir noch ein paar kurze Fragen an euch.

## Interrupts

**Welche Kategorien von Interrupts können allgemein unterschieden werden?**

  [[ ]] Gleitende Interrupts
  [[X]] Maskierbare Interrupts
  [[X]] nicht-maskierbare Interrupts
  [[X]] Inter-Prozessor Interrupts 
  [[ ]] Asynchrone Interrupts
  [[ ]] Synchrone Interrupts
  [[X]] Software Interrupts
  [[X]] Spurious Interrupts

**Was versteht man unter *nested interrupts*?**

  [( )] Programmabläufe, die lediglich aus Interrupts bestehen.
  [(X)] Interrupts, die während der Abarbeitung eines vorhergehenden Interrupts bearbeitet werden.
  [( )] Interrupts, die weitere Interrupts auslösen.
  [( )] Programmabläufe, die Interrupts aus Sicherheitsgründen verbieten.
        

**Welche Typen von Interrupts unterscheidet der AVR ATmega32U4?**

  [( )] Externe und interne Interrupts.
  [(X)] Interrupts mit und ohne Interrupt Flag.
  [( )] Kurze und Lange Interrupts.
  
**Was bezeichnet die *Interrupt Response Time*?**

  [( )] Die Abarbeitungsdauer eines Interrupts.
  [(X)] Die Zeit gemessen von dem Auftreten eines Interrupt-Signals bis zum Beginn der Abarbeitung der Interruptroutine.
  [( )] Die Zeit gemessen von dem Sprung aus dem Programmablauf bis zum Sprung zurück in den Programmablauf.

**Was ist die *Interrupt Response Time* des AVR ATmega32U4?**

  [(X)] 5 Clock Cylces
  [( )] 10 Clock Cylces
  [( )] 15 Clock Cylces

## Odometrie

**Welchen Vorteil hat ein Absolutwertgeben (engl. *absolute encoder*) gegenüber einem Inkrementalgeber (engl. *incremental encoder*)?**

  [(X)] Er ermöglicht die direkte Schätzung der Position eines Motors/Rades.
  [( )] Er kann bei höheren Frequenzen betrieben werden.
  [( )] Er hat einen einfacheren Aufbau.

## PID Regler

**Wofür steht PID im Namen des PID Reglers?**

  [( )] Passing, Integrating, Deviating 
  [(X)] Proportional, Integral, Derivative
  [( )] Proportional, Interfacing, Deviating 
  
**Wozu wird der I-Anteil in einem PID Regler benötigt?**

  [(X)] Um einen durch sinkende Fehlerwerte verbleibenden Regelfehler auszugleichen.
  [( )] Um das Verhalten des geregelten Systems auszugleichen.
  [( )] Um Fehler des Sensors auszugleichen.

## Bewegungsstrategien

**Wofür steht die Abkürzung SLAM?**

  [( )] Self-centered localization and motion
  [( )] Similarity based labeling and motion
  [( )] Simultaneous localization and motion
  [(X)] Simultaneous localization and mapping

# Abschlussfragebogen

Bearbeitungsdauer: 7-10 Minuten

Wir bitten Sie nun noch einen Abschlussfragebogen zur Lehrveranstaltung “PKeS” und insbesondere zur Arbeit mit dem RemoteLab (eLab - Webinterface) auszufüllen. Bitte nehmen Sie sich die Zeit! Für aussagekräftige Ergebnisse, die zur Verbesserung des RemoteLabs führen, sind wir auf Ihre Unterstützung angewiesen! 

**Wie würden Sie ihre Kenntnisse der folgenden Konzepte und Anwendungen NACH der Teilnahme an der LV “Prinzipien und Komponenten eingebetteter Systeme” einschätzen?**
 
**Meine Kenntnisse sind …**

[(:sehr gering)(:2)(:3)(:4)(:sehr hoch)]
    [ ] Roboteranwendungen (ROS/LegoMindStorm/...)
    [ ] Eingebettete Controller/Boards (Arduino/Raspberry PI/PIC/BeagleBone/...)
    [ ] Eingebettete Betriebssysteme (RTOS, Contiki, embedded Linux/ ...)
    [ ] SmartPhone-Anwendungen (Android/iOS/Windows)
    [ ] Web-Frontend (HTML/Javascript/...)
    [ ] Web-Backend (PHP/Perl/RubyRails/NodeJS/...)

**Hat Ihnen die Arbeit mit dem RemoteLab dabei geholfen, folgende Konzepte besser zu verstehen?**

[(:gar nicht)(:2)(:3)(:4)(:voll und ganz)]
    [                                                  ] Timer 
    [                                                  ] ALU
    [                                                  ] GPIO 
    [                                                  ] MemoryMappedI 
    [                                                  ] Flash
    [                                                  ] Ram
    [                                                  ] EEPROM
    [                                                  ] PWM
    [                                                  ] Interrupts
        
**Wie würden Sie Ihren Lernfortschritt (Verbesserung Ihrer Kenntnisse bzw. Fähigkeiten) im Rahmen der Arbeit mit dem RemoteLab einschätzen?**

[(:sehr gering)(:2)(:3)(:4)(:sehr hoch)]
    [ ] Systemnahe C-Programmierung (Registerzugriff, Bitmanipulationen, ...) 
    [ ] Grundlage sensorischer Umgebungserfassung (Wirkprinzipien/Modalitäten)         
    [ ] Signalverarbeitung (Filter/Transformation/Detektion)         
    [ ] Ansteuerung Aktuatorik (PWM/Steuerung & Regelung)    
    [ ] Grundlagen Eingebetteter Systeme (Physical Computing) 
    [ ] Verstehendes Lesen von Datenblättern        
 
**Gab es Aufgaben, wo Sie sich zusätzliche Hilfestellungen, Informationen oder Lernmaterialien gewünscht hätten? Beschreiben Sie diese kurz:**

[[___ ___ ___ ___]]

**Bitte schätzen Sie nun die Lehrveranstaltung “Prinzipien und Komponenten eingebetteter Systeme” anhand einiger Fragen ein. Möglicherweise kommen Ihnen einige Fragen aus der Lehrveranstaltungsevaluation bekannt vor, dennoch bitten wir Sie alle Fragen zu beantworten.

[(:sehr unzufrieden)(:2)(:3)(:4)(:sehr zufrieden)]
    [ ] Inwieweit sind Sie ganz allgemein mit der Lehrveranstaltung “eingebettete Systeme” (Vorlesung + Übung + Selbstlernphasen) zufrieden?

[(:deutlich niedriger)(:2)(:3)(:4)(:deutlich hoeher)]
    [ ] Im Vergleich mit anderen LV: Wie schätzen Sie das inhaltliche Niveau der Lehrveranstaltung ein?                       
    [ ] Im Vergleich mit anderen Lehrveranstaltungen: Wie hoch war der Zeitaufwand für diese LV? 

[(:sehr niedrig)(:2)(:3)(:4)(:sehr hoch)]
    [ ] Wie beurteilen Sie den Zeitaufwand für die Bearbeitung der praktischen Aufgaben?    

[(:sehr schlecht)(:2)(:3)(:4)(:sehr gut)]
    [ ] Wie beurteilen Sie die Klarheit der Beschreibungen der praktischen Aufgaben?

[(:gar nicht)(:2)(:3)(:4)(:sehr gut)]
    [ ] Wie gut fühlen Sie sich durch vorangegangene LV auf die Aufgaben im RemoteLab vorbereitet?           
    [ ] Wie gut fühlten Sie sich durch die Vorlesung auf die Aufgaben im RemoteLab vorbereitet?      
    [ ] Wie gut konnten Sie eine inhaltliche Beziehung zwischen den Inhalten der Vorlesung und den Aufgaben im RemoteLab herstellen?           
    [ ] Inwiefern war die Übung hilfreich, um die Lerninhalte zu verstehen?       
    [ ] Wie gut fühlten Sie sich durch die ÜbungsleiterInnen betreut?      
    [ ] Inwiefern waren Vorlesung, Aufgaben im RemoteLab und Übung zeitlich gut aufeinander abgestimmt?  
    [ ] Inwiefern fühlten Sie sich auf die mündliche Prüfung am Ende der LV gut vorbereitet?
    [ ] gar nicht- sehr häufig: Haben Sie sich an den Übungsleiter gewandt, wenn Sie konkrete Probleme oder Fragen hatten?     

**In den folgenden Fragen geht es um Ihre Einschätzung der Nützlichkeit des RemoteLabs.**

**Als wie effektiv schätzen Sie die Arbeit mit dem RemoteLab zu folgenden Lernzielen ein?**

[(:gar nicht effektiv)(:2)(:3)(:4)(:sehr effektiv)]
    [ ] Vermittlung von spezifischen Konzepten, die für das Verstehen der Thematik der LV “Prinzipien und Komponenten eingebetteter Systeme” relevant sind.            
    [ ] Verbesserung meines generellen Wissens über das Feld.   
    [ ] Verbesserung von Fähigkeiten, die eine(n) bessere(n) InformatikerIn/ProgrammiererIn aus mir machen.


**Die im RemoteLab erlernten Inhalte werden...**

[(:stimme gar nicht zu)(:2)(:3)(:4)(:stimme voll zu)]
    [ ] ...im Verlauf des weiteren Studiums von Nutzen sein.       
    [ ] ... bei der Stellensuche nach dem Studium relevant sein.     
    [ ] ... im Rahmen von Praktika zum Einsatz kommen.                        

**Sie hatten in dieser Lehrveranstaltung die Möglichkeit, die praktischen Aufgaben in einem RemoteLab zu bearbeiten. Vergleichen Sie die Arbeit mit einem RemoteLab bitte mit der Arbeit in einem Labor, in dem Sie vor Ort sind. Inwieweit stimmen Sie folgenden Aussagen zu:**

[(:gar nicht)(:2)(:3)(:4)(:voll und ganz)]
    [ ] Der Remote-Ansatz ermöglicht es mir, die Lernaufgaben schneller zu erledigen.
    [ ] Durch die Nutzung des RemoteLabs, kann ich mich intensiver mit den Lernaufgaben beschäftigen.      
    [ ] Durch die Nutzung des RemoteLabs erschließen sich mir die Inhalte der LV leichter.     
    [ ] Das RemoteLab ermöglicht mir eine größere zeitliche Flexibilität bei der Bearbeitung der Aufgaben.     
    [ ] Das RemoteLab ermöglicht mir eine größere örtliche Flexibilität bei der Bearbeitung der Aufgaben.      
    [ ] Bei der Arbeit mit dem RemoteLab vermisse ich den direkten Umgang mit den Robotern.         
    [ ] Der Zeitbedarf bei der Arbeit mit dem RemoteLab war höher, als bei der Arbeit im Labor vor Ort.        
    [ ] Die ständige Verfügbarkeit des RemoteLabs hat es mir ermöglicht, Lösungsansätze direkt auszuprobieren.

**Nun wollen wir wissen, in welchem Umfang Sie die Möglichkeit, von einem Ort Ihrer Wahl auf das Labor zuzugreifen, genutzt haben. An welchem Ort (außerhalb der Übungen) haben Sie mit dem RemoteLab gearbeitet?**

[(:Nie)(:2)(:3)(:4)(:Immer)]
    [ ] In Raum 334
    [ ] Von einem anderen Ort in der Uni
    [ ] Von außerhalb der Uni (zu Hause etc.)

**Wenn Sie auch außerhalb der Übung im Raum 334 gearbeitet haben, was waren die Gründe dafür? Bitte erläutern Sie diese kurz:**

[[___ ___ ___ ___]]

**Inwieweit stimmen Sie den folgenden Aussagen zur Bedienung des RemoteLab-System zu?**

[(:stimme gar nicht zu)(:2)(:3)(:4)(:stimme voll zu)]
    [ ] Das System ist unhandlich zu bedienen.
    [ ] Die Oberfläche des Remote Labs ist hilfreich strukturiert.
    [ ] Es ist einfach die Bedienung des Remote Lab-Systems zu erlernen.
    [ ] Die Bedienung des Remote Lab ist intuitiv.
    [ ] Mit dem Remote Lab-System zu arbeiten, erfordert (unabhängig von den konkreten Lernaufgaben) ein hohes Maß an geistiger Anstrengung.
    [ ] Die Interaktion mit dem Remote Lab-System ist klar und verständlich.
    [ ] Das Remote Lab gibt klare und verständliche Rückmeldungen.
    [ ] Die Fehlermeldungen des Remote Labs sind bei der Lösung von Problemen hilfreich.
    [ ] Es hat mich einige Anstrengung gekostet, zu verstehen, wie das System funktioniert.
    [ ] Ich finde, dass das Remote Lab-System insgesamt einfach zu bedienen ist.
    [ ] Für mich war die Nutzung des Remote Labs selbsterklärend.
    [ ] Das Remote Lab hat zuverlässig funktioniert.
    [ ] Die Roboter haben zuverlässig funktioniert.

**Nachdem Sie nun bereits die letzte Aufgabe in PKeS bearbeitet haben, möchten wir Sie um eine kurze Einschätzung ihrer Motivation insgesamt bitten. Inwiefern stimmen Sie folgenden Aussagen zur Arbeit mit dem Remote Lab zu?**

**Die Bearbeitung der Aufgaben …**

[(:stimme gar nicht zu)(:2)(:3)(:4)(:stimme voll zu)]
    [ ] ... war interessant.
    [ ] ... hat mir Spaß gemacht.
    [ ] ... hat mich neugierig auf die weitere Beschäftigung mit den Inhalten der LV gemacht.
    [ ] ... hat mich motiviert, mich mit den Inhalten der LV zu beschäftigen.
    [ ] ... hat mir gefallen.
    [ ] Solche Aufgaben würde ich gerne öfter bearbeiten.


**Und wie schätzen Sie Ihre kognitive Anstrengung bei der Bearbeitung der Aufgaben ein?**

[(:stimme gar nicht zu)(:2)(:3)(:4)(:stimme voll zu)]
    [ ] Inhaltlich waren die Aufgaben sehr komplex.
    [ ] Die Probleme, die es zu lösen galt, waren sehr schwierig.
    [ ] In den Aufgaben wurden sehr komplizierte Konzepte behandelt.
    [ ] Ich habe sehr viel kognitive Anstrengung investiert, um mit der Komplexität der Inhalte umzugehen.
    [ ] Die Aufgabenstellungen und Erklärungen in den Aufgaben waren schwer verständlich formuliert.
    [ ] Die Aufgabenstellungen und Erklärungen waren unklar formuliert.
    [ ] Die Aufgabenstellungen und Erklärungen waren für das Lernen ineffektiv.
    [ ] Ich habe sehr viel kognitive Anstrengung investiert, um unklare Aufgabenstellungen und Erklärungen zu verstehen.
    [ ] Die Aufgaben haben dazu beigetragen, dass ich die Inhalte, die behandelt wurden, besser verstanden habe.
    [ ] Die Aufgaben haben mich dabei unterstützt, die übergeordneten Problemstellungen zu verstehen.
    [ ] Die Aufgaben haben mein Wissen über die zugrundeliegenden Konzepte verbessert.
    [ ] Ich weiß nun sehr viel besser, wie solche Problemstellungen zu behandeln sind.
    [ ] Ich habe sehr viel kognitive Anstrengung investiert, um die Inhalte besser zu verstehen.

**Nun noch eine Frage zu Ihren Interessen an der Informatik. Bitte geben Sie an, welche der Aktivitäten Sie gerne ausführen und welche nicht.**

[(:sehr ungerne)(:2)(:3)(:4)(:sehr gerne)]
    [ ] Quellcode schreiben  
    [ ] Dokumentation lesen
    [ ] Rechner und Programme einrichten/konfigurieren  
    [ ] Assembler-Programmierung 
    [ ] Konsolen-Tools (Eingabeaufforderung)       
    [ ] Programmieren allgemein      
    [ ] Hardwarenahe Programmierung        
    [ ] Debugging      
    [ ] Datenbankenmanagement     
    [ ] Erschließen neuer Systeme    
    [ ] Lernen neuer Programmierparadigmen         

**Gleich haben Sie es geschafft! Zum Abschluss möchten wir Ihnen noch ein paar Fragen zur Zusammenarbeit mit anderen Studierenden stellen.**

[(:Nie)(:2)(:3)(:4)(:Immer)]
    [ ] Haben Sie bei der Bearbeitung der Aufgaben mit anderen Studierenden zusammengearbeitet?

**Wenn sie die mit KommilitonInnen zusammengearbeitet haben, ...**

[(:Ja immer)(:2)(:3)(:4)(:nein immer mit wechselnden )]
    [ ] ... haben Sie immer mit dem/den gleichen KommilitonInnen zusammengearbeitet?

**Wenn Sie mit KommilitonInnen zusammengearbeitet haben, was war Ihre Motivation dafür?**

[(:nicht wichtig)(:2)(:3)(:4)(:sehr wichtig)]
    [ ] Ich finde es wichtig, andere Studierende zu treffen. 
    [ ] Wir konnten uns die Arbeit aufteilen.
    [ ] Ich konnte dadurch von erfahreneren Studierenden lernen.
    [ ] Mit anderen Studierenden zusammen, macht die Bearbeitung der Aufgaben mehr Spaß.
    [ ] Wir konnten uns gegenseitig bei der Bearbeitung der Aufgaben helfen.

**Wenn Sie gemeinsam mit anderen Studierenden an den Aufgaben gearbeitet haben, welche Aussagen beschreiben Ihre gemeinsame Arbeit im RemoteLab?**

[(:nie)(:2)(:3)(:4)(:immer)]
    [ ] Wir haben gemeinsam am gleichen Ort vor dem gleichen Rechner gearbeitet.
    [ ] Wir haben am gleichen Ort aber vor unterschiedlichen Rechnern gearbeitet.
    [ ] Wir haben gleichzeitig an unterschiedlichen Orten gearbeitet und standen dabei in Kontakt (per Mail, social messenger, Skype o.a.).
    [ ] Wir haben zu unterschiedlichen Zeiten an unterschiedlichen Orten gearbeitet und uns regelmäßig ausgetauscht.

**Wie haben Sie bei der Bedienung des RemoteLabs zusammengearbeitet?**

[(:Meistens durch mich)(:2)(:3)(:4)(:Meistens durch KommilitonInnen)]
    [ ] Die Bedienung des RemoteLabs erfolgte ...
    [ ] Das Schreiben des Codes erfolgte ...
    [ ] Der Austausch mit den Tutoren (und anderen Lehrpersonen) erfolgte ...

**Wir danken Ihnen für Ihre Unterstützung bei der Studie! Wenn Sie an den Ergebnissen interessiert sind, geben Sie bitte Prof. Zug Bescheid. Sobald ein Ergebnisbericht vorliegt, würden wir diesen dann an Sie weiterleiten.**
