<!--

author:   Georg Jäger

email:    gjaeger@ovgu.de

version:  1.0.0

language: de

narrator:  Deutsch Female



@run_main
<script>
events.register("@0", e => {
		if (!e.exit)
    		send.lia("output", e.stdout);
		else
    		send.lia("eval",  "LIA: stop");
});

send.handle("input", (e) => {send.service("@0",  {input: e})});
send.handle("stop",  (e) => {send.service("@0",  {stop: ""})});


send.service("@0", {start: "CodeRunner", settings: null})
.receive("ok", e => {
		send.lia("output", e.message);

		send.service("@0", {files: {"main.c": `@input`}})
		.receive("ok", e => {
				send.lia("output", e.message);

				send.service("@0",  {compile: "gcc main.c -o a.out", order: ["main.c"]})
				.receive("ok", e => {
						send.lia("log", e.message, e.details, true);

						send.service("@0",  {execute: "./a.out"})
						.receive("ok", e => {
								send.lia("output", e.message);
								send.lia("eval", "LIA: terminal", [], false);
						})
						.receive("error", e => { send.lia("log", e.message, e.details, false); send.lia("eval", "LIA: stop"); });
				})
				.receive("error", e => { send.lia("log", e.message, e.details, false); send.lia("eval", "LIA: stop"); });
		})
		.receive("error", e => { send.lia("output", e.message); send.lia("eval", "LIA: stop"); });
})
.receive("error", e => { send.lia("output", e.message); send.lia("eval", "LIA: stop"); });

"LIA: wait";
</script>

@end







@sketch
<script>
events.register("mc_stdout", e => { send.lia("output", e); });
events.register("mc_start", e => { send.lia("eval", "LIA: terminal"); });


let compile = `arduino-builder -compile -logger=machine -hardware /usr/local/share/arduino/hardware -tools /usr/local/share/arduino/tools-builder -tools /usr/local/share/arduino/hardware/tools/avr -built-in-libraries /usr/local/share/arduino/libraries -libraries /usr/local/share/arduino/libraries -fqbn="Robubot Micro:avr:microbot" -ide-version=10807 -build-path $PWD/build -warnings=none -prefs=build.warn_data_percentage=100 -prefs=runtime.tools.avr-gcc.path=/usr/local/share/arduino/packages/arduino/tools/avr-gcc/4.8.1-arduino5/avr -prefs=runtime.tools.avr-gcc-4.8.1-arduino5.path=/usr/local/share/arduino/packages/arduino/tools/avr-gcc/4.8.1-arduino5/avr sketch/sketch.ino`;


send.service("@0arduino", {start: "CodeRunner", settings: null})
.receive("ok", e => {

		send.lia("output", e.message);
    send.service("@0arduino", {files: {"sketch/sketch.ino": `@input(0)`,
                                       "sketch/Distance.h": `@input(1)`,
                                       "sketch/Distance.cpp": `@input(2)`,
                                       "sketch/everytime.h": `@input(3)`,
                                       "sketch/IMU.h": `@input(4)`,
                                       "sketch/IMU.cpp": `@input(5)`,
                                       "sketch/IMURegisters.h": `@input(6)`,
                                       "sketch/Motor.h": `@input(7)`,
                                       "sketch/Motor.cpp": `@input(8)`,
                                       "sketch/Odometry.h": `@input(9)`,
                                       "sketch/Odometry.cpp": `@input(10)`,
                                       "sketch/PID.h": `@input(11)`,
                                       "sketch/PID.cpp": `@input(12)`,
                                       "build/": ""}})
		.receive("ok", e => {

				send.lia("output", e.message);
				send.service("@0arduino",  {compile: compile, order: ["sketch.ino", "Distance.h", "Distance.cpp", "everytime.h", "IMU.h", "IMU.cpp", "IMURegisters.h", "Motor.h", "Motor.cpp", "Odometry.h", "Odometry.cpp", "PID.h", "PID.cpp"]})
				.receive("ok", e => {

						send.lia("log", e.message, e.details, true);
            if(!window["bot_selected"]) { send.lia("eval", "LIA: stop"); }
            else {

              document.getElementById("button_"+window.bot_selected).disabled = true;
              send.service("c",
                { connect: [["@0arduino", {"get_path": "build/sketch.ino.hex"}], ["mc", {"upload": null, "target": window["bot_selected"]}]]
                }
              );

              send.handle("input", (e) => {
                send.service("mc",  {id: "bot_stdin",
                           action: "call",
                           params: {procedure: "com.robulab.target."+window["bot_selected"]+".send_input", args: [0,  String.fromCharCode(0)+btoa(e) ] }})});

              send.handle("stop",  (e) => {
                document.getElementById("button_"+window.bot_selected).disabled = false;

                send.service("mc", {id: "stdio0",
                                   action: "unsubscribe",
                                   params: {id: window["stdio0"], args: [] }});

                send.service("mc", {id: "stdio1",
                                   action: "unsubscribe",
                                   params: {id: window["stdio1"], args: [] }});

                send.service("mc",  {id: "bot_disconnect."+window["bot_selected"],
                           action: "call",
                           params: {procedure: "com.robulab.target.disconnect", args: [window["bot_selected"]] }})});


                delete window.stdio0;
                delete window.stdio1;
            }
				})
				.receive("error", e => { send.lia("log", e.message, e.details, false); send.lia("eval", "LIA: stop"); });
		})
		.receive("error", e => { send.lia("output", e.message); send.lia("eval", "LIA: stop"); });
})
.receive("error", e => { send.lia("output", e.message); send.lia("eval", "LIA: stop"); });

"LIA: wait";
</script>

<script>
function aduinoview_init() {

  let arduino_view_frame = document.getElementById("arduinoviewer");

  window["arduino_view_frame"] = arduino_view_frame;

  arduino_view_frame.onload = function () {

    arduino_view_frame.contentWindow.ArduinoView.init = function () {

      arduino_view_frame.contentWindow.ArduinoView.sendMessage = function(e) {

        send.service("mc",  {id: "bot_stdin2",
                   action: "call",
                   params: {procedure: "com.robulab.target."+window["bot_selected"]+".send_input", args: [1,  String.fromCharCode(0)+btoa(e) ] }});
      };

      arduino_view_frame.contentWindow.ArduinoView.onInputPermissionChanged(true);
    };
  };

  arduino_view_frame.contentWindow.ArduinoView.sendMessage = function(e) {

    send.service("mc",  {id: "bot_stdin2",
               action: "call",
               params: {procedure: "com.robulab.target."+window["bot_selected"]+".send_input", args: [1,  String.fromCharCode(0)+btoa(e) ] }});
  };

  arduino_view_frame.contentWindow.ArduinoView.onInputPermissionChanged(true);

}

function update() {
  if(!window["bot_selected"]) {
    for(let i=0; i<window.bot_list.length; i++) {
      let btn = document.getElementById("button_"+window.bot_list[i].target);

      if(btn === null) {
        let cmdi = document.getElementById("bot_list");

        btn = document.createElement("BUTTON");
        btn.id = "button_" + window.bot_list[i].target;
        btn.innerHTML = window.bot_list[i].name;
        btn.onclick = function() {
           let id = window.bot_list[i].target;

           send.service("mc", {id: "bot_connect."+id,
                      action: "call",
                      params: {procedure: "com.robulab.target.connect", args: [id] }});
         };

         cmdi.appendChild(btn);
         cmdi.appendChild(document.createTextNode(" "));
      }

      btn.style.backgroundColor = "";

      if(window.bot_list[i].owner == ""){
        btn.className = "lia-btn";
        btn.disabled = false;
      }
      else {
        btn.className = "lia-btn";
        btn.disabled = true;
      }
    }
  }
  else {
    for(let i=0; i<window.bot_list.length; i++) {
      let btn = document.getElementById("button_"+window.bot_list[i].target);
      if(window.bot_list[i].target == window["bot_selected"]){
        btn.className = "lia-btn";
        btn.disabled = false;
        btn.style.backgroundColor = "#ADFF2F";
      }
      else {
        btn.className = "lia-btn";
        btn.disabled = true;
      }
    }
  }
}


function subscriptions() {
    events.register("mc", e => {
       if(typeof(e) === "undefined")
          return;

       if(!!e.subscription) {
         if (e.subscription.startsWith("com.robulab.livestream")) {
            //console.log("vid", e.parameters.args[0].data);
            window.cam.PutData(new Uint8Array(e.parameters.args[0].data));
         }
         else if (e.subscription == "com.robulab.target.changed") {
           let args = e.parameters.args;
           for(let i=0; i<args.length; i++) {
             for(let j=0; j<window.bot_list.length; j++) {
               if(args[i].target == window.bot_list[j].target) {
                 window.bot_list[j] = Object.assign(window.bot_list[j], args[i])
               }
             }
           }
           update();
         } else if (e.subscription.endsWith(".0.raw_out")) {
           events.dispatch("mc_stdout", String.fromCharCode.apply(this, e.parameters.args[0].data));
         } else if (e.subscription.endsWith(".1.raw_out")) {
           window["arduino_view_frame"].contentWindow.ArduinoView.onArduinoViewMessage(
             String.fromCharCode.apply(this, e.parameters.args[0].data) );
         }
         else {
          //console.log("problem", e);
         }
       }
       else if(e.id == "bot_list") {
         window["bot_list"] = e.ok;
         let cmdi = document.getElementById("bot_list");

         for (let i = 0; i< window.bot_list.length; i++) {
            let btn = document.createElement("BUTTON");
            btn.id = "button_" + window.bot_list[i].target;
            btn.innerHTML = window.bot_list[i].target;
            btn.onclick = function() {
              let id = window.bot_list[i].target;

              send.service("mc", {id: "bot_connect."+id,
                           action: "call",
                          params: {procedure: "com.robulab.target.connect", args: [id] }});
              };

              cmdi.appendChild(btn);
              cmdi.appendChild(document.createTextNode(" "));

              send.service("mc", {id: "bot_name."+i,
                           action: "call",
                           params: {procedure: "com.robulab.target."+ window.bot_list[i].target +".get_name", args: [] }});
             }
           update();
         }
         else if( e.id.startsWith("bot_flash1") ) {
           let [cmd, target, file] = e.id.split(" ");
           send.service("mc", {upload: file, target: target, id: e.ok});
         }
         else if( e.id.startsWith("bot_flash2") ) {
           let [cmd, target, id] = e.id.split(" ");
           send.service("mc", {finish: parseInt(id), target: target});
         }
         else if ( e.id.startsWith("bot_flash3") && !e.ok ) {
           aduinoview_init();
           let [cmd, target, id] = e.id.split(" ");

           send.service("mc", {id: "bot_stdio.0.target",
                               action: "subscribe",
                               params: {topic: "com.robulab.target."+target+".0.raw_out", args: [] }});

           send.service("mc", {id: "bot_stdio.1.target",
                               action: "subscribe",
                               params: {topic: "com.robulab.target."+target+".1.raw_out", args: [] }});

           events.dispatch("mc_start", "");


        }

        else if (e.id.startsWith("bot_stdio")) {
          let [cmd, stdio, target] = e.id.split(".");
          window["stdio"+stdio] = e.ok.id;
        }

        else if( e.id.startsWith("bot_name") ) {
          let [cmd, id] = e.id.split(".")
          window.bot_list[id]["name"] = e.ok;
          document.getElementById("button_"+window.bot_list[id].target).innerHTML = e.ok;
        }

        else if( e.id.startsWith("bot_connect") ) {
          let [cmd, target] = e.id.split(".");
          window["bot_selected"] = target;
          let btn = document.getElementById("button_"+target);
          btn.onclick = function() {
              send.service("mc", {id: "bot_disconnect."+target,
                         action: "call",
                         params: {procedure: "com.robulab.target.disconnect", args: [target] }});
          };

          send.service("mc", {id: "bot_stream."+target,
                     action: "call",
                     params: {procedure: "com.robulab.target."+target+".get_stream", args: [target] }});

          update();

          window["cam"] = window.decoder(document.getElementById("bot_show"), ([w, h]) => {
              let canvas = document.getElementById("bot_show");

              canvas.height = h;
              canvas.width = w;
          });
        }
        else if( e.id.startsWith("bot_disconnect") ) {
          let [cmd, target] = e.id.split(".");
          delete window["bot_selected"];

          let btn = document.getElementById("button_"+target)
          btn.onclick = function() {
              send.service("mc", {id: "bot_connect."+target,
                         action: "call",
                         params: {procedure: "com.robulab.target.connect", args: [target] }});
          };

          send.service("mc", {id: "cam_disconnect",
                             action: "unsubscribe",
                             params: {id: window["streaming_id"], args: [] }});

          update();
        }
        else if ( e.id.startsWith("bot_stream") ) {
          let [cmd, target] = e.id.split(".");
          send.service("mc",
                       {id: "streaming",
                        action: "subscribe",
                        params: {topic: e.ok.url, args: [] }});
        }
        else if (e.id == "streaming") {
          window["streaming_id"] = e.ok.id;
        }

        else if ( e.id == "cam_disconnect") {
          delete window.cam;
        }

        else {
          console.log("not handled", e);
      }
      });

      send.service("mc", {id: "bot_list",
                            action: "call",
                            params: {procedure: "com.robulab.target.get-online", args: [] }});

      send.service("mc", {id: "bot_changes",
                            action: "subscribe",
                            params: {topic: "com.robulab.target.changed", args: [] }})

      send.service("mc", {id: "bot_changes",
                            action: "subscribe",
                            params: {topic: "com.robulab.target.reregister", args: [] }})


      window["mc_subscribed"] = true;

};

function login(silent=true) {
    let cmdi = document.getElementById("mcInterface");

    send.service("mc", {start: "MissionControl", settings: null})
      .receive("ok", (e) => {
          console.log("user-connected:", e);
          cmdi.hidden = false;
          window["mc_logged_in"] = true;
          subscriptions();
      })
      .receive("error", (e) => {
          cmdi.hidden = true;
          console.log("Error user-connected:", e);
          alert("Fail: Please check your login!");
      });
};

if (!window.mc_logged_in) {
  setTimeout((e) => { login() }, 300);
}
else {
  document.getElementById("mcInterface").hidden = false;
  update();
}

window.addEventListener("beforeunload", function (event) {

    if ( window.stdio1 ) {
          send.service("mc", {id: "stdio1",
                       action: "unsubscribe",
                       params: {id: window["stdio1"], args: [] }});
    }

    if ( window.stdio0 ) {
          send.service("mc", {id: "stdio0",
                       action: "unsubscribe",
                       params: {id: window["stdio0"], args: [] }});
    }

    if(window.bot_selected) {
        send.service("mc", {id: "cam_disconnect",
                           action: "unsubscribe",
                           params: {id: window["streaming_id"], args: [] }});

        send.service("mc",  {id: "bot_disconnect."+window["bot_selected"],
                   action: "call",
                   params: {procedure: "com.robulab.target.disconnect", args: [window["bot_selected"]] }});
    }

});

</script>


<div id="mcInterface" class="row" hidden="true">
  <span class="col-xs-12 col-md-6" style="border-style: solid">
    <span id="bot_list" ></span>
    <br>
    <iframe id="arduinoviewer" style="margin-left: 3px; width: 99%; max-height: 432px;" src="https://elab.ovgu.robulab.com/arduinoview"></iframe>
  </span>
  <span class="col-xs-12 col-md-6" style="border-style: solid; overflow: auto">
    <canvas id="bot_show" style="width: calc(16 * 10vw); height: calc(9 * 10vw);"></canvas>
  </span>
</div>
@end


@init_clear
<script>
  let cmdi = document.getElementById("mcInterface");
  if(cmdi)
    cmdi.hidden = true;

  let viewer = document.getElementById("arduinoviewer");
  if(viewer)
    try {
    //  viewer.contentWindow.document.body.innerHTML = ""
    } catch(e) {}

  if ( window.stdio1 ) {
          send.service("mc", {id: "stdio1",
                       action: "unsubscribe",
                       params: {id: window["stdio1"], args: [] }});
  }

  if ( window.stdio0 ) {
          send.service("mc", {id: "stdio0",
                       action: "unsubscribe",
                       params: {id: window["stdio0"], args: [] }});
  }

  if(window.bot_selected) {
        send.service("mc", {id: "cam_disconnect",
                           action: "unsubscribe",
                           params: {id: window["streaming_id"], args: [] }});

        send.service("mc",  {id: "bot_disconnect."+window["bot_selected"],
                   action: "call",
                   params: {procedure: "com.robulab.target.disconnect", args: [window["bot_selected"]] }});
  }
</script>


@end







-->

# PKeS 4

@init_clear

Willkommen zurück im eLearning-System *eLab*.

    --{{0}}--
In der letzten Aufgabe habt ihr bereits zwei wichtige Sensortypen
(Distanzsensoren, IMU) kennengelernt. Während ihr mit Hilfe der Distanzsensoren
die Umgebung des Roboters wahrnehmen könnt, erlaubt euch die IMU Bewegungen des
Roboters selber festzustellen.

    --{{1}}--
Neben der IMU, wird vor allem aber auch die Odometrie genutzt, um die Ausführung
von Bewegungsbefehlen eines Roboters zu überwachen. Daher wird das erste Ziel
dieser Aufgabe sein, die Odometrie unseres Roboters in Betrieb zu nehmen.

    --{{2}}--
Nachdem wir dann alle relevanten Sensoren und Aktoren unseres Roboters auslesen
bzw. ansteuern können, nutzen wir diese Möglichkeiten zur Umsetzung einer
autonomen Bewegungsstrategie.

    --{{3}}--
Ziel wird es sein, sowohl eine Kollisionsvermeidung, als auch einen
[Wall Follower](https://en.wikipedia.org/wiki/Maze_solving_algorithm) zu
implementieren. Während die Kollisionsvermeidung sicherstellt, dass unser
Roboter nicht frontal mit einem Objekt kollidiert, erlaubt die Wandverfolgung
unserem Roboter sich (theoretisch) durch ein Labyrinth zu finden.

![maze](pics/maze.gif)<!--
style="display:block; margin-left: auto; margin-right: auto;"
-->


## Themen und Ziele

@init_clear

    --{{0}}--
Das Ziel dieser Aufgabe ist einen *Wall Follower* zu implementieren. Da dieser
von unserem Roboter verlangt, nicht nur seine Umgebung wahrzunehmen, sondern
auch seine Bewegungen zu planen und korrekt auszuführen, muss zunächst ein
weitere Sensor, die Odometrie, in Betrieb genommen werden. Da sie auf der
Abhandlung von Interrupts basiert, bilden diese einen Schwerpunkt der Aufgabe.

    --{{1}}--
Mit Hilfe der Odometrie wird es uns ermöglicht, zu überprüfen, ob unser Roboter
auch unsere Steuerungskommandos so ausführt, wie wir sie geplant haben. Wie uns
auffallen wird, ist das nicht immer der Fall, sodass wir eine weiter Komponente
zur Motoransteuerung hinzufügen müssen: eine Regelung.

    --{{2}}--
Zwar gibt es eine Vielzahl von Möglichkeiten eine Regelung umzusetzen, weit
verbreitet sind allerdings die PID-Regler. Daher werden wir uns auf diese
beschränken.

    --{{3}}--
Letztlich bieten uns die verschiedenen Komponenten unseres Roboters nun die
Möglichkeit eine autonome Bewegungsstrategie zu implementieren. Daher bildet das
Thema des *Motion Planning* den letzten Schwerpunkt dieser Aufgabe.


**Themen:**

* Interrupts
* Odometrie
* PID Regler
* Motion Planning


**Ziel(e):**

* {{1}} Implementierung einer Interrupt-basierten Odometrie
* {{2}} Geschwindigkeitsregelung der Motoren mit Hilfe von PID Reglern
* {{3}} Implementierung einer Bewegungsstrategie zur Wandverfolgung.


## Weitere Informationen

@init_clear

**Interrupts:**

    --{{0}}--
Da zur korrekten Implementierung der Odometrie die Konfiguration und Nutzung von
Interrupts benötigt wird, haben wir euch, neben der Vorlesung, weitere
einführende Referenzen aufgelistet.

    {{0}}
* [Mikrocontroller.net](https://www.mikrocontroller.net/articles/Interrupt)
* [Übersicht](https://en.wikipedia.org/wiki/Interrupt)


**Odometrie:**

    --{{1}}--
Darüber hinaus könnt ihr Informationen sowohl zur Odometrie als auch zu
Rotationsencoder finden.

    {{1}}
* [Odometrie](https://en.wikipedia.org/wiki/Odometry)
* [Rotationsencoder](https://en.wikipedia.org/wiki/Rotary_encoder)


**PID-Controller:**

    --{{2}}--
Wie schon erwähnt sollen die Daten der Odometrie zur Geschwindigkeitsregelung
der Motoren genutzt werden. Daher haben wir auch einführende Artikel zu PID
Reglern aufgelistet.

    {{2}}
* [Einfache Einführung](https://www.csimn.com/CSI_pages/PIDforDummies.html)
* [Überblick](https://en.wikipedia.org/wiki/PID_controller)
* Einführende Erklärungen:

  !?[PIDIntroduction](https://www.youtube.com/embed/UR0hOmjaHp0)<!--
    width="560px" height="315px" -->


**Robotik:**

    --{{3}}--
Letztlich sind die bisher besprochenen Komponenten unseres Roboters lediglich
Voraussetzungen zur Umsetzung von autonomen Bewegungsstrategien und
Verhaltensmustern. Da diese allerdings nicht in dieser Veranstaltung behandelt
werden können, wollen wir euch zumindest Ansatzpunkte für weitere Recherchen
geben. Zum einen bietet das (allgemeine) Gebiet der autonomen Roboter
vielseitige Herausforderungen die zunehmend durch Algorithmen und Methodiken der
künstlichen Intelligenz gelöst werden. Zum anderen bilden aber vor allem die
Bereiche des *Simultaneous localization and mapping (SLAM)* und des
*Motion Planning* konkrete Bereiche der Robotik, die grundlegend für die
Autonomie von Robotern sind.


    {{3}}
* [Allgemeine Einführung in das Gebiet der autonomen Roboter](https://github.com/correll/Introduction-to-Autonomous-Robots/releases/download/v1.9/book.pdf)
* *Simultaneous localization and mapping (SLAM):*
  * [Übersicht bei Wikipedia](https://en.wikipedia.org/wiki/Simultaneous_localization_and_mapping)
  * [Vorlesung zu SLAM (YouTube)](https://www.youtube.com/watch?v=U6vr3iNrwRA&list=PLgnQpQtFTOGQrZ4O5QzbIHgl3b1JHimN_)
  * Visualisierung von SLAM:

      !?[SimplePresentationSLAM](https://www.youtube.com/embed/SeNLUW79_-c)<!-- width="560px" height="315px" -->


* *Motion Planning:*

  * [Vorlesungsfolien](https://ipvs.informatik.uni-stuttgart.de/mlr/marc/teaching/14-Robotics/04-pathPlanning.pdf)
  * Erklärungen zu Motion Planning in relation zu SLAM:

    !?[Visualisierungsvideo](https://www.youtube.com/embed/qXZt-B7iUyw?list=PLKwqbLx4DLDGkT0Tc6yiqsjDmH395hgvF)<!--
      width="560px" height="315px" -->



**PKeS:**

    --{{4}}--
Wie immer findet ihr hier aber auch die Datenblätter der Komponenten unserer
Roboterplattform.

    {{4}}
* [Datenblatt IMU (MPU-9255)](https://stanford.edu/class/ee267/misc/MPU-9255-Datasheet.pdf)
* [Datenblatt IMU (MPU-9255) Register Map](https://stanford.edu/class/ee267/misc/MPU-9255-Register-Map.pdf)
* [Datenblatt infrarot Distanzsensor Sharp GP2Y0A41SK0F](http://sharp-world.com/products/device/lineup/data/pdf/datasheet/gp2y0a41sk_e.pdf)
* [Datenblatt infrarot Distanzsensor Sharp GP2Y0A02YK0F](http://sharp-world.com/products/device/lineup/data/pdf/datasheet/gp2y0a02yk_e.pdf)
* [Datenblatt Motortreiber](http://www.st.com/content/ccc/resource/technical/document/datasheet/59/d0/ce/41/56/bb/4b/10/DM00034699.pdf/files/DM00034699.pdf/jcr:content/translations/en.DM00034699.pdf)
* [Schaltbelegungsplan](https://github.com/liaScript/PKeS0/blob/master/materials/robubot_stud.pdf?raw=true)
* [Arduinoview](https://github.com/fesselk/Arduinoview/blob/master/doc/Documetation.md)
* [Datenblatt des AVR ATmega32U4](http://www.atmel.com/Images/Atmel-7766-8-bit-AVR-ATmega16U4-32U4_Datasheet.pdf)
* [Interrupt Reference in der AVR Libc](http://www.atmel.com/webdoc/avrlibcreferencemanual/group__avr__interrupts.html)



# Aufgabe 4

@init_clear

                                    --{{0}}--
Um die Bearbeitung der Aufgabe wieder etwas einfacher zu gestalten und zu
strukturieren, kann sie in folgenden Teilaufgaben aufgeteilt werden.

**Teilaufgaben**

* {{1}} _Implementierung der Odometrie._

                                    --{{1}}--
  Wie schon beschrieben, besteht die erste Teilaufgabe darin, die Odometrie des
  Roboters in Betrieb zu nehmen. Dazu müsst ihr vor allem die Möglichkeiten der
  *Pin Change Interrupts* nutzen.

* {{2}} _PID-Regelung der Geschwindigkeit der Motoren._

                                    --{{2}}--
  Basierend auf den Daten könnt ihr dann eine Geschwindigkeitsregelung für die
  Motoren (links und rechts) implementieren.

* {{3}} _Kollisionsvermeidung auf Basis der IR-Distanzsensoren._

                                    --{{3}}--
  Nachdem wir dann die Umgebung unseres Roboters wahrnehmen können und darauf
  reagieren können, sollte das erste Ziel unserer Bewegungsstrategie sein, eine
  Kollisionsvermeidung zu implementieren. So soll verhindert werden, dass unser
  Roboter mit einem Objekt kollidiert.

* {{4}} _Wandverfolgung._

                                    --{{4}}--
  Da die Kollisionsvermeidung allerdings lediglich reaktiv ist, wollen wir
  darüber hinaus auch eine aktive Bewegungsstrategie implementieren. In unserem
  Fall soll der Roboter eine Wandverfolgung durchführen.


> **Hinweis:**
>
> Verwendet bei der Bearbeitung der Aufgabe keine Funktionen aus der
> Arduino-Bibliothek. Lediglich die Funktionen der `Serial`-Klasse zur
> Ansteuerung der seriellen Schnittstelle, die Funktionen `millis()` und
> `micros()` zur einfachen Zeitmessung, sowie die Funktionen aus `Wire.h` für
> die I^2^C Kommunikation können genutzt werden.


## Teilaufgabe 1

@init_clear

                                  --{{1}}--
Das Ziel dieser Aufgabe ist es, die Odometrie an unserem Roboter in Betrieb zu
nehmen. Dazu müsst ihr zunächst die entsprechenden Interrupts konfigurieren und
eine Interruptroutine schreiben, um die Ticks der inkrementeller
Rotationsencoder auszulesen.

                                  --{{2}}--
Durch die Interrupts ist die Funktionalität der Odometrie bereits implementiert.
Um ihre Daten auch im weiteren Programm nutzbar zu machen, benötigen wir noch
ein entsprechendes Interface. Implementiert dazu die Funktion `odomTicks`, wie
sie durch `Odometry.h` vorgegeben ist. Sie sollte die Anzahl von Ticks für beide
Motoren zurückgeben. Bei der Implementierung dieser Funktion ist zu beachten,
dass keine weiteren Interrupts das Auslesen der Register stören, da so
inkonsistente Daten entstehen könnten.

                                  --{{3}}--
Da *Ticks* ein hardwarenahes Maß ist, wollen wir zur weiteren Verwendung daraus
in einem dritten Schritt die Geschwindigkeit und zurückgelegte Distanz
berechnen. Implementiert dazu die in `Odometry.h` vorgegebenen
Konvertierungsfunktionen `odomVelocity` und `odomDistance`. Die Geschwindigkeit
sollte in $\frac{m}{s}$ während die Distanz in $m$ zurückgegeben werden sollte.


**Ziel:** Implementierung der Odometrie.


**Teilschritte:**

* {{1}} Konfiguration der Interrupts (`initOdom()`) und Implementierung der
  _Interrupt-Service-Rountine_.
* {{2}} Implementiert die Funktion `odomTicks(struct OdomData& d)`.
* {{3}} Implementiert die Funktionen `odomVelocity(int32_t dticks, float dtime)`
  und `odomDistance(int32_t dticks)`


                                     {{3}}
> **Hinweis:**
>
> Der Durchmesser für ein Rad beträgt *65mm*. Die Odometrie jedes Rades besteht
> aus
> [inkrementellen Rotationsgebern](https://en.wikipedia.org/wiki/Rotary_encoder#Incremental_rotary_encoder).
> Bei jeder Radumdrehung durchlaufen diese 224,20 Perioden in 4 Phasen. Beachtet
> die verschiedenen Drehrichtungen der Räder!


## Teilaufgabe 2

@init_clear

                                    --{{0}}--
Die Odometrie, die wir in der vorhergehenden Aufgabe implementiert haben,
erlaubt es uns nun, die Geschwindigkeit der Motoren/Räder zu regeln. Dazu sollt
ihr in dieser Teilaufgabe einen PID-Regler implementieren und parametrisieren.

**Ziel:**

Regelt die Drehgeschwindigkeit der Motoren mit Hilfe eines PID Regler.

**Teilschritte:**

* {{1}} Implementierung einen PID-Regler in der `PID.h`-Datei.

                                      --{{1}}--
  Implementiert im ersten Schritt die Funktionalität eines PID-Reglers. Dies
  soll separat passieren, daher haben wir euch die leere `PID.h`-Datei
  vorgegeben.

* {{2}} Implementierung der Interface-Funktionen `stopMotors()` und
  `startMotors()`.

                                      --{{2}}--
  In einem zweiten Schritt wollen wir nun die eigentliche Steuerung der Motoren
  durch den PID-Regler abstrahieren. Implementiert dazu die Funktionen der
  `MotorControl.h`. Beginnt mit den Funktionen `stopMotors` und `startMotors`.
  Wie schon in der Aufgabe 2, soll zunächst die Möglichkeit geschaffen werden,
  die Motoren jederzeit zu stoppen. Dies soll durch die entsprechende Funktion
  `stopMotors` sichergestellt werden. Achtet darauf, dass ihr die Motoren mit
  Hilfe dieser Funktion zu jeder Zeit deaktivieren könnt!

                                      --{{2}}--
  Das Gegenstück zu `stopMotors` bildet die Funktion `startMotors` mit deren
  Hilfe ihr die Motoren freigeben und aktivieren können solltet.

* {{3}} Implementierung der Interface-Funktionen
  `setVelocityMotors(float left, float right)` und `updateMotors()`.

                                      --{{3}}--
  Nun könnt ihr die Ansteuerung und Regelung der Motoren durch einen PID-Regler
  implementieren. Mit der Funktion `setVelocityMotors` sollen die
  Zielgeschwindigkeiten für die Räder vorgegeben werden können. Da der
  PID-Regler nun regelmäßig entsprechende Steuerkommandos an die Motoren
  weitergeben muss damit diese Zielgeschwindigkeiten auch erreicht werden, soll
  während der `updateMotors` Funktion die Berechnung der Steuersignale für die
  Motoren stattfinden und an die Motoren weitergeben werden.

* {{4}} Parametrisierung des Reglers für beide Motoren.

                                      --{{4}}--
  Zur Schonung der Motoren sollt ihr folgende Regel beachten: Wenn Ist- und
  Soll-Geschwindigkeiten aller Motoren 0 sind und die Steuersignale für die
  Motoren zwischen -15 und 15 liegt, werden die Motoren deaktiviert. Weicht
  einer der genannten Prameter ab, sind die Motoren wieder zu aktivieren.

                                      --{{5}}--
  Letztlich müsst ihr noch die Parameter des PID-Reglers bestimmen. Dazu könnt
  ihr zum einen die
  [Ziegler-Nichols Methode](https://en.wikipedia.org/wiki/Ziegler%E2%80%93Nichols_method)
  verwenden, zum anderen könnt ihr versuchen eine Geradeausfahrt durch eine gute
  Parametrisierung zu erreichen.

## Teilaufgabe 3

@init_clear

                                  --{{0}}--
Mit der Regelungsstrategie unserer Motoren können wir nun eine autonome
Bewegungsstrategie implementieren. Da dies bedeutet, dass unser Roboter autonom
die Geschwindigkeitsvorgaben für die Motoren bestimmen wird, müssen wir dabei
auch die Sicherheit des Systems beachten.

**Ziel:** Nutzt die IR-Distanzsensoren um frontale Kollisionen zu vermeiden.

**Teilschritte:**

* {{1}} Implementiert eine Schutzfunktion zur Vermeidung von frontalen
  Kollisionen.

                                --{{1}}--
  Allgemein bedeutet dies, dass wir zum Beispiel Kollisionen unseres Roboters
  mit Objekten in der Umgebung vermeiden sollten. Da uns allerdings nur
  Distanzsensoren an der Front unseres Roboters zur Verfügung stehen, können wir
  zunächst auch nur frontale Kollisionen vermeiden.

* {{2}} Legt empirisch einen minimalen Distanzwert für die Kollisionsvermeidung
  fest.

                                --{{2}}--
  Daher ist das Ziel dieser Teilaufgabe, eine Kollisionsvermeidung auf Basis der
  IR-Distanzsensoren zu implementieren.

## Teilaufgabe 4

@init_clear

                                --{{0}}--
Nachdem unsere Sensoren und Aktoren, sowie eine einfache Kollisionsvermeidung
einsatzbereit sind, können wir diese Komponenten nutzen um eine weitere
Bewegungsstrategie zu implementieren: eine Wandverfolgung.

**Ziel:**

Implementiert eine Wandverfolgung.

**Teilschritte:**

* {{1}} Implementiert einen Button in Arduinoview zum Umschalten zwischen
  manuellen und autonomen Modus

                                --{{1}}--
  Implementiert zunächst einen Button in Arduinoview um zwischen dem manuellen
  und dem autonomen Modus umschalten zu können. Beachtet, dass vor allem der
  *Stop* Button in jedem Fall zum Stoppen des Roboters genutzt werden können
  sollte.

* {{2}} Implementiert eine initiale Lokalisierung: "Wo ist die nächste Wand?"

                                --{{2}}--
  Ein Problem beim Lösen dieser Aufgabe ist, dass unsere Distanzsensoren
  lediglich an der Front unseres Roboters montiert sind. Da wir dementsprechend
  nicht direkt unsere aktuelle, seitliche Distanz zur Wand messen können, müsst
  ihr den Roboter von Zeit zu Zeit erneut orientieren.

* {{3}} Plant eine Bewegung zur Wand, sodass ihr in einer Distanz von *10-20cm*
  neben der Wand steht.

                                --{{3}}--
  Eine Möglichkeit zur Umsetzung wäre: Ihr könnt die IMU nutzen und den Roboter
  auf der Stelle drehen lassen. So könnt ihr die naheliegendste Wand und eure
  Distanz zu dieser detektieren.

* {{4}} Beginnt die Wandverfolgung. Eine Möglichkeit wäre:

                                --{{4}}--
  Wenn ihr die Distanz zu naheliegendsten Wand kennt, könnt ihr abhängig davon
  eure Bewegung planen. Das heißt in welche Richtung ihr euch bewegen wollt
  (Soll die Wand auf der linken oder rechten Seite des Roboters sein?) und wie
  ihr möglichst parallel zur Wand fahren könnt. Überlegt außerdem, wann es nötig
  sein wird den Roboter erneut zu orientieren.

  ---
  {{5}} __1__ Plant eine Teilstrecke entlang der Wand und berechnet die
              entsprechenden Steuerkommandos

                              --{{5}}--
  Durch wiederholtes Planen der Bewegungsstrategie und Re-Orientieren des
  Roboters sollte es euch möglich sein, der Wand in einer Distanz zwischen
  *10cm* und *20cm* zu folgen.

  ---
  {{6}} __2__ Re-orientiert den Roboter um eine weiter Teilstrecke zu planen.

                              --{{6}}--
  Während der Implementierung werdet ihr mehrere Parameter, die das Verhalten
  eures Roboters beeinflussen werden, bestimmen. Legt diese in Abhängigkeit zur
  Ungenauigkeit eurer Sensorik und Aktorik fest. Dies wird voraussichtlich ein
  empirisches Vorgehen erfordern.

  ---
  {{7}} __3__ Wiederholt Schritte __1__ und __2__ um euch entlang der Wand zu
              bewegen.

                              --{{7}}--
  Das hier vorgestellte Vorgehen zur Wandverfolgung ist nur eine Möglichkeit.
  Wir sind gespannt, welche Lösungen ihr findet!


# **Programmierung**

@init_clear

``` cpp sketch.ino
// -----------------------------------------------------------------
// Exercise 4
// -----------------------------------------------------------------

#include <FrameStream.h>
#include <Frameiterator.h>
#include <avr/io.h>

#define OUTPUT__BAUD_RATE 9600
FrameStream frm(Serial1);

// Forward declarations
void InitGUI();

// hierarchical runnerlist, that connects the gui elements with
// callback methods
declarerunnerlist(GUI);

// First level will be called by frm.run (if a frame is recived)
beginrunnerlist();
fwdrunner(!g, GUIrunnerlist); //forward !g to the second level (GUI)
callrunner(!!, InitGUI);
fwdrunner(ST, stickdata);
endrunnerlist();

// GUI level
beginrunnerlist(GUI);
callrunner(es, CallbackSTOP);
callrunner(ms, CallbackSTART);
endrunnerlist();

void stickdata(char* str, size_t length)
{
    // str contains a string of two numbers ranging from -127 to +127
    // indicating left and right motor values
    //TODO: interprete str, set motors
}

void CallbackSTOP()
{
    // call stopMotors();
}

void CallbackSTART()
{
    // call startMotors()
}

/*
 * @brief initialises the GUI of ArduinoView
 *
 * In this function, the GUI, is configured. For this, Arduinoview shorthand,
 * HTML as well as embedded JavaScript can be used.
 */
void InitGUI()
{
    delay(500);

    frm.print(F("!SbesvSTOP"));
    frm.end();

    frm.print(F("!SbmsvSTART"));
    frm.end();


    //this implements a joystick field using HTML SVG and JS
    //and some info divs next to it
    frm.print(F("!H"
      "<div><style>svg * { pointer-events: none; }</style>\n"
        "<div style='display:inline-block'> <div id='state'> </div>\n"
        "<svg id='stick' width='300' height='300' viewBox='-150 -150 300 300' style='background:rgb(200,200,255)' >\n"
        "<line id='pxy' x1='0' y1='0' x2='100' y2='100' style='stroke:rgb(255,0,0);stroke-width:3' />\n"
        "<circle id='cc' cx='0' cy='0' r='3'  style='stroke:rgb(0,0,0);stroke-width:3' />\n"
      "</svg></div>"
      "<div style='display:inline-block'>"
        "<div id='info0'></div>"
        "<div id='info1'></div>"
        "<div id='info2'></div>"
      "</div></div>\n"
      "<script>\n"
        "var getEl=function(x){return document.getElementById(x)};\n"
        "var setEl=function(el,attr,val){(el).setAttributeNS(null,attr,val)};\n"
        "var stick=getEl('stick');\n"
        "function sticktransform(x,y){\n"
          "x = x-150;\n"
          "y = -(y-150);\n"
          "setstick(x,y);\n"
        "}\n"
        "function setstick(x,y){\n"
          "setpointer(x,y);\n"
          "l = Math.floor(Math.min(127,Math.max(-127,y + x/2)));\n"
          "r = Math.floor(Math.min(127,Math.max(-127,y - x/2)));\n"
          "setStateDisplay(x,y,l,r);\n"
          "sendframe(\"ST\"+l +\",\"+r);\n"
        "}\n"
        "function setStateDisplay(x,y,l,r){\n"
          "msg=getEl('state');\n"
          "msg.innerHTML= 'x= '+ x +' y= '+ y +' l= '+ l +' r= '+ r ;\n"
        "}\n"
        "function setpointer(x,y){\n"
          "pxy=getEl('pxy');\n"
          "setEl(pxy,'x2',x);\n"
          "setEl(pxy,'y2',-y);\n"
        "}\n"
        "stick.onmousemove=function(e){\n"
          "if( e.buttons == 1 ){\n"
            "sticktransform(e.offsetX,e.offsetY);\n"
          "}};\n"
          "stick.onmousedown=function(e){\n"
            "if( e.buttons == 1 ){\n"
              "sticktransform(e.offsetX,e.offsetY);\n"
            "}};\n"
          "stick.onmouseup=function(e){\n"
            "setstick(0,0);\n"
          "};\n"
          "stick.onmouseleave=function(e){\n"
            "setstick(0,0);\n"
          "};\n"
          "function sendframe(msg){\n"
            "ArduinoView.sendMessage(ArduinoView._createSerialFrame(msg))\n"
          "}\n"
          "setstick(0,0);\n"
        "</script>\n"));
    frm.end();
}

/*
 * @brief Initialisation
 *
 * Implement basic initialisation of the program.
 */
void setup()
{
    delay(1000);

    //prepare Serial interfaces
    Serial.begin(OUTPUT__BAUD_RATE);
    Serial1.begin(OUTPUT__BAUD_RATE);

    Serial.println(F("Willkommen zur PKeS Übung"));

    Serial.println(F("PKes4"));

    //request reset of GUI
    frm.print("!!");
    frm.end();

    delay(500);

    // TODO initialize Odometry, Sensors, Motors, etc.
}

/*
 *  @brief Main loop
 *
 *  This function will be called repeatedly and shall therefore implement the
 *  main behavior of the program.
 */
void loop()
{
    // read & run ArduinoView Frames
    while(frm.run());
}
```
``` cpp -Distance.h
#ifndef ESSIRSENSOR_H
#define ESSIRSENSOR_H

#include <inttypes.h>

void     configADC();
uint16_t linearizeSR(uint16_t );
uint16_t linearizeLR(uint16_t );
int16_t readADC(uint8_t );

#endif
```
``` cpp Distance.cpp
#include "Distance.h"
#include <Arduino.h>
#include <avr/io.h>

uint16_t ADC_mV_Faktor;

/**
 * @brief Configures the ADC related to mode, clock speed, gain
 *        resolution and refrence voltage
 *
 *
 * chose the reference voltage carfully related to the output
 * range of the sensor.
 */
void configADC()
{
}

/**
 * @brief Starts a single conversion and receives a result return in mV
 */
int16_t readADC(uint8_t channel)
{
    int16_t mV;
    return mV;
}

/**
 * @brief Maps the digital voltage information on a distance
 *        in mm
 */
uint16_t linearizeSR(uint16_t distmV)
{
}

/**
 * @brief Maps the digital voltage information on a distance
 *        in mm
 */
uint16_t linearizeLR(uint16_t distmV)
{
}
```
``` cpp -everytime.h
#ifndef EVERYTIMEH24b0433
#define EVERYTIMEH24b0433
/* Copyright (c) 2017, Karl Fessel, lib everytime, All rights reserved.*/

// these macros run a command or block in regular intervals

/**
 * these macros depend on unspecified but very common behavior of C, C++ and
 * the target architectur:
 * - integer overflow for subtraction and addition
 * - if the return type of the time function is bigger than the storage
 *   the lower bits schould be stored
 *   (this behavior is specified for values that fit into the storrage type
 *    but is unspecified for values that are bigger)
 */

/* simple versions for easy reading:
 * #define every(X) static uint16_t before = 0; uint16_t now = millis();\
 *               if((now - before >= X) && (before = now,1) )
 *
 *     static uint32_t before = 0;
 *     unsigned long now = millis();
 *     if (now - before > 1000){
 *     before = now;
 */

//this make the variable names more unique by adding the current line number
#define IDCAT3( A, B) A ## B
#define IDCAT2( A, B) IDCAT3( A, B)
#define IDCAT(X) IDCAT2(X, __LINE__)

/**
 * these are gereric variants to create the mor specifc ones
 * X      is the time betwen two runs
 * START  is the time of the first start or -1 to wait for the first perion after time 0 (-1 saves memory and flash)
 * STARTO is the time of the first start with offset from first test (now) (0 to start now)
 * F      is the function that returns some kind of time
 *  e.g.: Arduinos millis() and micros()
 *  !! millis() do not include all milliseconds since its incremented in
 *  !!   256 / 250 kHz steps and are compensated every ~42 milliseconds by adding another millisecond !!
 *  !! micros() function itself takes more than a microsecond to execute !!
 * T      is the type of the storage variable the type should behave as said above
 *
 * everySTF(X, START, F, T)
 *  start with offset from time 0, -1 -> first intevall
 *
 * everyNTF(X, STARTO, F, T)
 *  start with offset from now
 */
#ifndef ET_DRIFT
// Dirft Mode
// 1 : drift if miss
//      e.g.: trigger every 5, miss at 10 execution, starts at 11 next trigger will be 16
// 2 : never drift but may loose its function if execution takes to long
//      e.g.: trigger every 5, miss at 10 and 15 -> double trigger
// 3 : no drift until big miss then drift to now
//      e.g.: trigger every 5, miss at 10 and 15 -> trigger once at 16 next trigger will be 21
// 4 : try to stay in messure, if big miss advance in steps
//      e.g.: trigger every 5, miss at 10 and 15 trigger once at 16 next trigger will be 20
#define ET_DRIFT 3
#endif

#if ET_DRIFT == 1
// 34 Byte for everySTF(X, -1, millis(), uint16_t )
// drift if miss
// non dynamic initialisation of static variable saves space in memory and flash 0 saves even more
#define everySTF(X, START, F, T) \
            T IDCAT(now) = (F);\
            static T IDCAT(before) = ((START) < 0) ? 0 : (0 - (X) + (START));\
            if( (IDCAT(now) - IDCAT(before) >= (X)) \
             && ((IDCAT(before) = IDCAT(now)),1) )

// start with offset from now
#define everyNTF(X, STARTO, F, T) \
            T IDCAT(now) = (F);\
            static (T) IDCAT(before) = IDCAT(now) - (X) + (STARTO);\
            if( (IDCAT(now) - IDCAT(before) >= (X)) \
             && ((IDCAT(before) = IDCAT(now)),1) )

#endif
#if ET_DRIFT == 2
// 38 Byte for everySTF(X, -1, millis(), uint16_t )
// no drift but may lose its function if miss is to big
// non dynamic initialisation of static variable saves space in memory and flash 0 saves even more
#define everySTF(X, START, F, T) \
            T IDCAT(now) = F;\
            static T IDCAT(before) = (START < 0) ? 0 : (0 - X + START);\
            if( (IDCAT(now) - IDCAT(before) >= X) \
             && ((IDCAT(before) += X),1) )

// start with offset from now
#define everyNTF(X, STARTO, F, T) \
            T IDCAT(now) = F;\
            static T IDCAT(before) = IDCAT(now) - X + STARTO;\
            if( (IDCAT(now) - IDCAT(before) >= X) \
             && ((IDCAT(before) += X),1) )
#endif
#if ET_DRIFT == 3
// 46 Byte for everySTF(X, -1, millis(), uint16_t )
// drift when big miss (two intervals) else no drift
// non dynamic initialisation of static variable saves space in memory and flash 0 saves even more
#define everySTF(X, START, F, T) \
            T IDCAT(now) = F;\
            static T IDCAT(before) = (START < 0) ? 0 : (0 - X + START);\
            if( (IDCAT(now) - IDCAT(before) >= X) \
              && ((IDCAT(before) = (IDCAT(now) - IDCAT(before) > 2 * X)?IDCAT(now):IDCAT(before) + X),1) )

// start with offset from now
#define everyNTF(X, STARTO, F, T) \
            T IDCAT(now) = F;\
            static T IDCAT(before) = IDCAT(now) - X + STARTO;\
            if( (IDCAT(now) - IDCAT(before) >= X) \
             && ((IDCAT(before) = (IDCAT(now) - IDCAT(before) > 2 * X)?IDCAT(now):IDCAT(before) + X),1) )
#endif
#if ET_DRIFT == 4
// 68 Bytes for everySTF(X, -1, millis(), uint16_t )
// this uses lambda functions for the caluculation of the increment
// lambda functions are part of c++11
#define everySTF(X, START, F, T) \
            T IDCAT(now) = F;\
            static T IDCAT(before) = (START < 0) ? 0 : (0 - X + START);\
            T IDCAT(dist) = IDCAT(now) - IDCAT(before);\
            if( (IDCAT(dist) >= X) \
             && (IDCAT(before) += [&](){ T step = 0; do{ step += X; }while( (IDCAT(dist) > step + X) && (step + X > step) ); return step; }() ,1  ))

// start with offset from now
#define everyNTF(X, STARTO, F, T) \
            T IDCAT(now) = F;\
            static T IDCAT(before) = IDCAT(now) - X + STARTO;\
            T IDCAT(dist) = IDCAT(now) - IDCAT(before);\
            T IDCAT(step) = 0;\
            if( (IDCAT(dist) >= X) \
             && (IDCAT(before) += [&](){ T step = 0; do{ step += X; }while( (IDCAT(dist) > step + X) && (step + X > step) ); return step; }() ,1  ))
#endif

// execute every X milliseconds (8 bit)
#define everyb(X)            everySTF(X, -1, millis(), uint8_t )
#define everynowb(X)         everyNTF(X,  0, millis(), uint8_t )

// execute every X milliseconds (16 bit)
#define every(X)            everySTF(X, -1, millis(), uint16_t )
#define everynow(X)         everyNTF(X,  0, millis(), uint16_t )

// execute every X milliseconds (32 bit)
#define everyl(X)            everySTF(X, -1, millis(), uint32_t )
#define everynowl(X)         everyNTF(X,  0, millis(), uint32_t )

// execute every X microseconds (16 bit)
// !! loop cycle may be longer on most arduinos than the resolution of this !!
// (30 microseconds with this just toggeling a LED using arduino method digitalWrite)
#define everyu(X)            everySTF(X, -1, micros(), uint16_t )
#define everynowu(X)         everyNTF(X,  0, micros(), uint16_t )

// execute every X microseconds (32 bit)
#define everyul(X)            everySTF(X, -1, micros(), uint32_t )
#define everynowul(X)         everyNTF(X,  0, micros(), uint32_t )

#endif
```
``` cpp -IMU.h
#ifndef IMU_H
#define IMU_H

#include <inttypes.h>

#define IMU_ADDR  0x69

struct IMUdata
{
    int16_t accel_x;
    int16_t accel_y;
    int16_t accel_z;
    int16_t gyro_x;
    int16_t gyro_y;
    int16_t gyro_z;
};

struct IMUdataf
{
    float accel_x;
    float accel_y;
    float accel_z;
    float gyro_x;
    float gyro_y;
    float gyro_z;
};

void initializeIMU();
void readIMU(struct IMUdata& d);
uint8_t IMUWhoAreYou();
void readIMUscale(struct IMUdataf& d);
#endif
```
``` cpp IMU.cpp
#include <Arduino.h>
#include <Wire.h>
#include "IMU.h"
#include "IMURegisters.h"

#define IMU_Addr  0b1101001
#define I2C_clock 400000UL

#define ACCEL_FS_SEL 00
#define GYRO_FS_SEL 00

uint8_t IMU_WhoAmI;

void initializeIMU()
{
    uint8_t ret;
    //I2C Master
    Wire.begin();

    Wire.setClock(I2C_clock);

    //read WhoAmI
    Wire.beginTransmission(IMU_ADDR);
    Wire.write(IMU_WHO_AM_I);
    ret = Wire.endTransmission(false);

    if(ret)
    {
        IMU_WhoAmI=ret;
        return;
    }

    Wire.requestFrom(IMU_ADDR,1);
    IMU_WhoAmI=Wire.read();

    //disable powersave modes
    Wire.beginTransmission(IMU_ADDR);
    Wire.write(IMU_PWR_MGMT_1);
    Wire.write(0);
    //and _PWR_MGMT_2
    Wire.write(0);
    Wire.endTransmission();
//configuration
    Wire.beginTransmission(IMU_ADDR);
    Wire.write(IMU_GYRO_CONFIG);
    Wire.write(0|GYRO_FS_SEL<<3);
//  (IMU_ACCEL_CONFIG);
    Wire.write(0|ACCEL_FS_SEL<<3);
    Wire.endTransmission();
    //selftest ?
}

void readIMU(struct IMUdata& d)
{
    //read accelleromenter, temprature and gyroscope
    Wire.beginTransmission(IMU_ADDR);
    Wire.write(IMU_ACCEL_XOUT_H);
    Wire.endTransmission(false);
    Wire.requestFrom(IMU_ADDR,14);
    d.accel_x = Wire.read() << 8;
    d.accel_x |= Wire.read();
    d.accel_y = Wire.read() << 8;
    d.accel_y |= Wire.read();
    d.accel_z = Wire.read() << 8;
    d.accel_z |= Wire.read();
    //skip Temperature
    Wire.read();
    Wire.read();
//     Wire.beginTransmission(IMU_ADDR);
//     Wire.write(IMU_GYRO_XOUT_H);
//     Wire.endTransmission(false);
//     Wire.requestFrom(IMU_ADDR,6);
    d.gyro_x = Wire.read() << 8;
    d.gyro_x |= Wire.read();
    d.gyro_y = Wire.read() << 8;
    d.gyro_y |= Wire.read();
    d.gyro_z = Wire.read() << 8;
    d.gyro_z |= Wire.read();
}

void readIMUscale(struct IMUdataf& d)
{
}

uint8_t IMUWhoAreYou()
{
    return IMU_WhoAmI;
}
```
``` cpp -IMURegisters.h
#pragma once

#define IMU_SELF_TEST_X_GYR     0x00 /*  0*/
#define IMU_SELF_TEST_Y_GYR     0x01 /*  1*/
#define IMU_SELF_TEST_Z_GYR     0x02 /*  2*/
#define IMU_SELF_TEST_X_ACC     0x0D /* 13*/
#define IMU_SELF_TEST_Y_ACC     0x0E /* 14*/
#define IMU_SELF_TEST_Z_ACC     0x0F /* 15*/
#define IMU_XG_OFFSET_H         0x13 /* 19*/
#define IMU_XG_OFFSET_L         0x14 /* 20*/
#define IMU_YG_OFFSET_H         0x15 /* 21*/
#define IMU_YG_OFFSET_L         0x16 /* 22*/
#define IMU_ZG_OFFSET_H         0x17 /* 23*/
#define IMU_ZG_OFFSET_L         0x18 /* 24*/
#define IMU_SMPLRT_DIV          0x19 /* 25*/
#define IMU_CONFIG              0x1A /* 26*/
#define IMU_GYRO_CONFIG         0x1B /* 27*/
#define IMU_ACCEL_CONFIG        0x1C /* 28*/
#define IMU_ACCEL_CONFIG_2      0x1D /* 29*/
#define IMU_LP_ACCEL_ODR        0x1E /* 30*/
#define IMU_WOM_THR             0x1F /* 31*/
#define IMU_FIFO_EN             0x23 /* 35*/
#define IMU_I2C_MST_CTRL        0x24 /* 36*/
#define IMU_I2C_SLV0_ADDR       0x25 /* 37*/
#define IMU_I2C_SLV0_REG        0x26 /* 38*/
#define IMU_I2C_SLV0_CTRL       0x27 /* 39*/
#define IMU_I2C_SLV1_ADDR       0x28 /* 40*/
#define IMU_I2C_SLV1_REG        0x29 /* 41*/
#define IMU_I2C_SLV1_CTRL       0x2A /* 42*/
#define IMU_I2C_SLV2_ADDR       0x2B /* 43*/
#define IMU_I2C_SLV2_REG        0x2C /* 44*/
#define IMU_I2C_SLV2_CTRL       0x2D /* 45*/
#define IMU_I2C_SLV3_ADDR       0x2E /* 46*/
#define IMU_I2C_SLV3_REG        0x2F /* 47*/
#define IMU_I2C_SLV3_CTRL       0x30 /* 48*/
#define IMU_I2C_SLV4_ADDR       0x31 /* 49*/
#define IMU_I2C_SLV4_REG        0x32 /* 50*/
#define IMU_I2C_SLV4_DO         0x33 /* 51*/
#define IMU_I2C_SLV4_CTRL       0x34 /* 52*/
#define IMU_I2C_SLV4_DI         0x35 /* 53*/
#define IMU_I2C_MST_STATUS      0x36 /* 54*/
#define IMU_INT_PIN_CFG         0x37 /* 55*/
#define IMU_INT_ENABLE          0x38 /* 56*/
#define IMU_INT_STATUS          0x3A /* 58*/
#define IMU_ACCEL_XOUT_H        0x3B /* 59*/
#define IMU_ACCEL_XOUT_L        0x3C /* 60*/
#define IMU_ACCEL_YOUT_H        0x3D /* 61*/
#define IMU_ACCEL_YOUT_L        0x3E /* 62*/
#define IMU_ACCEL_ZOUT_H        0x3F /* 63*/
#define IMU_ACCEL_ZOUT_L        0x40 /* 64*/
#define IMU_TEMP_OUT_H          0x41 /* 65*/
#define IMU_TEMP_OUT_L          0x42 /* 66*/
#define IMU_GYRO_XOUT_H         0x43 /* 67*/
#define IMU_GYRO_XOUT_L         0x44 /* 68*/
#define IMU_GYRO_YOUT_H         0x45 /* 69*/
#define IMU_GYRO_YOUT_L         0x46 /* 70*/
#define IMU_GYRO_ZOUT_H         0x47 /* 71*/
#define IMU_GYRO_ZOUT_L         0x48 /* 72*/
#define IMU_EXT_SENS_DATA_00    0x49 /* 73*/
#define IMU_EXT_SENS_DATA_01    0x4A /* 74*/
#define IMU_EXT_SENS_DATA_02    0x4B /* 75*/
#define IMU_EXT_SENS_DATA_03    0x4C /* 76*/
#define IMU_EXT_SENS_DATA_04    0x4D /* 77*/
#define IMU_EXT_SENS_DATA_05    0x4E /* 78*/
#define IMU_EXT_SENS_DATA_06    0x4F /* 79*/
#define IMU_EXT_SENS_DATA_07    0x50 /* 80*/
#define IMU_EXT_SENS_DATA_08    0x51 /* 81*/
#define IMU_EXT_SENS_DATA_09    0x52 /* 82*/
#define IMU_EXT_SENS_DATA_10    0x53 /* 83*/
#define IMU_EXT_SENS_DATA_11    0x54 /* 84*/
#define IMU_EXT_SENS_DATA_12    0x55 /* 85*/
#define IMU_EXT_SENS_DATA_13    0x56 /* 86*/
#define IMU_EXT_SENS_DATA_14    0x57 /* 87*/
#define IMU_EXT_SENS_DATA_15    0x58 /* 88*/
#define IMU_EXT_SENS_DATA_16    0x59 /* 89*/
#define IMU_EXT_SENS_DATA_17    0x5A /* 90*/
#define IMU_EXT_SENS_DATA_18    0x5B /* 91*/
#define IMU_EXT_SENS_DATA_19    0x5C /* 92*/
#define IMU_EXT_SENS_DATA_20    0x5D /* 93*/
#define IMU_EXT_SENS_DATA_21    0x5E /* 94*/
#define IMU_EXT_SENS_DATA_22    0x5F /* 95*/
#define IMU_EXT_SENS_DATA_23    0x60 /* 96*/
#define IMU_I2C_SLV0_DO         0x63 /* 99*/
#define IMU_I2C_SLV1_DO         0x64 /*100*/
#define IMU_I2C_SLV2_DO         0x65 /*101*/
#define IMU_I2C_SLV3_DO         0x66 /*102*/
#define IMU_I2C_MST_DELAY_CTRL  0x67 /*103*/
#define IMU_SIGNAL_PATH_RESET   0x68 /*104*/
#define IMU_MOT_DETECT_CTRL     0x69 /*105*/
#define IMU_USER_CTRL           0x6A /*106*/
#define IMU_PWR_MGMT_1          0x6B /*107*/
#define IMU_PWR_MGMT_2          0x6C /*108*/
#define IMU_FIFO_COUNTH         0x72 /*114*/
#define IMU_FIFO_COUNTL         0x73 /*115*/
#define IMU_FIFO_R_W            0x74 /*116*/
#define IMU_WHO_AM_I            0x75 /*117*/
#define IMU_XA_OFFSET_H         0x77 /*119*/
#define IMU_XA_OFFSET_L         0x78 /*120*/
#define IMU_YA_OFFSET_H         0x7A /*122*/
#define IMU_YA_OFFSET_L         0x7B /*123*/
#define IMU_ZA_OFFSET_H         0x7D /*125*/
#define IMU_ZA_OFFSET_L         0x7E /*126*/
```
``` cpp -Motor.h
#pragma once

#include <inttypes.h>

// initialise timer(s) here
void initMotors();
// control the motor-power, pay attention to the direction of rotation
void setMotors(int8_t left, int8_t right);

// you may use this to set Motor-DDRs
void deactivateMotors();
void activateMotors();
```
``` cpp Motor.cpp

```
``` cpp -Odometry.h
#pragma once

#include <avr/io.h>
#include <avr/interrupt.h>
#include <inttypes.h>

//Arduino.h has some Pi
#include <Arduino.h>

struct OdomData
{
    int32_t left;
    int32_t right;
};

// initialises Odometrie
void initOdom();

// writes count courrent of ticks to structure
void odomTicks(struct OdomData&);

// m <- count
static float odomDistance( int32_t dticks)
{
}

// m/s <- count, millis
// you may want to use parts of millis for short messurements
static float odomVelocity( int32_t dticks, float dtime)
{
}
```
``` cpp Odometry.cpp
#include "Odometry.h"
```
``` cpp -PID.h
#pragma once

struct PID
{
    float kp, ki, kd, maxi, mini;
    struct
    {
        float d,i;
    } state;
};


void PIDInit(struct PID& pid,float kp,float ki,float kd,float maxi,float mini);

float PIDCalculate(struct PID& pid,float setpoint, float value);
```
``` cpp PID.cpp
#include "PID.h"

void PIDInit(struct PID& pid,float kp,float ki,float kd,float maxi,float mini)
{
#define SET(X) pid.X=X
    SET(kp);
    SET(ki);
    SET(kd);
    SET(maxi);
    SET(mini);
#undef SET
}

float PIDCalculate(struct PID& pid,float setpoint, float value)
{
    float ret;
    float error = setpoint - value;

    float p = pid.kp * error;
    ret = p;

    float i = pid.state.i + error;
    float maxi= pid.maxi;
    float mini= pid.mini;
    //clamp i
    i = (i>maxi)?(maxi):((i<mini)?(mini):(i));
    ret += pid.ki * i;
    pid.state.i = i;

    float d = value - pid.state.d;
    ret += pid.kd * d;
    pid.state.d = value;

    return ret;
}
```
@sketch





# Quizze

@init_clear

    --{{0}}--
Wie auch in der letzten Aufgabe haben wir noch ein paar kurze Fragen an euch.

## Interrupts

@init_clear

    --{{0}}--
Welche Kategorien von Interrupts können allgemein unterschieden werden?

    {{0-1}}
    [[ ]] Gleitende Interrupts
    [[X]] Maskierbare Interrupts
    [[X]] nicht-maskierbare Interrupts
    [[X]] Inter-Prozessor Interrupts
    [[ ]] Asynchrone Interrupts
    [[ ]] Synchrone Interrupts
    [[X]] Software Interrupts
    [[X]] Spurious Interrupts


    --{{1}}--
Was versteht man unter *nested interrupts*?

    {{1-2}}
    [( )] Programmabläufe, die lediglich aus Interrupts bestehen.
    [(X)] Interrupts, die während der Abarbeitung eines vorhergehenden Interrupts bearbeitet werden.
    [( )] Interrupts, die weitere Interrupts auslösen.
    [( )] Programmabläufe, die Interrupts aus Sicherheitsgründen verbieten.


    --{{2}}--
Welche Typen von Interrupts unterscheidet der AVR ATmega32U4?

    {{2-3}}
    [( )] Externe und interne Interrupts.
    [(X)] Interrupts mit und ohne Interrupt Flag.
    [( )] Kurze und Lange Interrupts.


    --{{3}}--
Was bezeichnet die *Interrupt Response Time*?

    {{3-4}}
    [( )] Die Abarbeitungsdauer eines Interrupts.
    [(X)] Die Zeit gemessen von dem Auftreten eines Interrupt-Signals bis zum Beginn der Abarbeitung der Interruptroutine.
    [( )] Die Zeit gemessen von dem Sprung aus dem Programmablauf bis zum Sprung zurück in den Programmablauf.


    --{{4}}--
Was ist die *Interrupt Response Time* des AVR ATmega32U4?

    {{4}}
    [(X)] 5 Clock Cylces
    [( )] 10 Clock Cylces
    [( )] 15 Clock Cylces

## Odometrie

@init_clear

    --{{0}}--
Welchen Vorteil hat ein Absolutwertgeber (*absolute encoder*) gegenüber einem
Inkrementalgeber (*incremental encoder*)?

    [(X)] Er ermöglicht die direkte Schätzung der Position eines Motors/Rades.
    [( )] Er kann bei höheren Frequenzen betrieben werden.
    [( )] Er hat einen einfacheren Aufbau.

## PID Regler

@init_clear

    --{{0}}--
Wofür steht PID im Namen des PID Reglers?

    {{0-1}}
    [( )] Passing, Integrating, Deviating
    [(X)] Proportional, Integral, Derivative
    [( )] Proportional, Interfacing, Deviating


    --{{1}}--
Wozu wird der I-Anteil in einem PID Regler benötigt?

    {{1}}
    [(X)] Um einen durch sinkende Fehlerwerte verbleibenden Regelfehler auszugleichen.
    [( )] Um das Verhalten des geregelten Systems auszugleichen.
    [( )] Um Fehler des Sensors auszugleichen.

## Bewegungsstrategien

@init_clear

    --{{0}}--
Wofür steht die Abkürzung SLAM?

    [( )] Self-centered localization and motion
    [( )] Similarity based labeling and motion
    [( )] Simultaneous localization and motion
    [(X)] Simultaneous localization and mapping

# Abschlussfragebogen

@init_clear

Bearbeitungsdauer: 7-10 Minuten

Wir bitten Sie nun noch einen Abschlussfragebogen zur Lehrveranstaltung “PKeS”
und insbesondere zur Arbeit mit dem RemoteLab (eLab - Webinterface) auszufüllen.
Bitte nehmen Sie sich die Zeit! Für aussagekräftige Ergebnisse, die zur
Verbesserung des RemoteLabs führen, sind wir auf Ihre Unterstützung angewiesen!

Wie würden Sie ihre Kenntnisse der folgenden Konzepte und Anwendungen NACH der
Teilnahme an der LV "Prinzipien und Komponenten eingebetteter Systeme"
einschätzen?

**Meine Kenntnisse sind ...**

    [(:sehr gering)(:2)(:3)(:4)(:sehr hoch)]
    [                                      ] Roboteranwendungen (ROS/LegoMindStorm/...)
    [                                      ] Eingebettete Controller/Boards (Arduino/Raspberry PI/PIC/BeagleBone/...)
    [                                      ] Eingebettete Betriebssysteme (RTOS, Contiki, embedded Linux/ ...)
    [                                      ] SmartPhone-Anwendungen (Android/iOS/Windows)
    [                                      ] Web-Frontend (HTML/Javascript/...)
    [                                      ] Web-Backend (PHP/Perl/RubyRails/NodeJS/...)


Hat Ihnen die Arbeit mit dem RemoteLab dabei geholfen, folgende Konzepte besser zu verstehen?

    [(:gar nicht)(:2)(:3)(:4)(:voll und ganz)]
    [                                        ] Timer
    [                                        ] ALU
    [                                        ] GPIO
    [                                        ] MemoryMappedIO
    [                                        ] Flash
    [                                        ] Ram
    [                                        ] EEPROM
    [                                        ] PWM
    [                                        ] Interrupts

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
