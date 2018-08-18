Fork von Babelweb -- Monitoring-Tool für den Babel Routing Dienst
=================================================================

Dies ist eine textlich für Freifunk-Franken-Gateways angepasste
und ins Deutsche übersetzte Version von BabelWeb 0.4.0.

Hinweis: In der Originaldokumentation wird als Kontaktadresse Gabriel Kerneis
angegeben. Dies ist nicht mehr korrekt, weil er die Entwicklung von BabelWeb
und ebenso den Support dafür eingestellt hat! Es gibt ein neues Team, welches
eine Nachfolgeversion entwickelt: https://github.com/Vivena/babelweb2


Installation (hier für Debian 9 beschrieben)
------------

Eine vollwertige Version von nodejs wird in Debian 9 nicht mehr angeboten,
weshalb es aus der Originalquelle installiert werden muss.

    apt-get install curl
    curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
    apt-get install nodejs

Nun wird das originale BabelWeb 0.4.0 aus NPM installiert.

    npm install -g babelweb
    npm update -g

Um Verwechslungen zu vermeiden, sollte das Original-Verzeichnis "static"
vor dem Installieren des Forks gelöscht oder umbenannt werden.
Das originale README.md kann umbenannt werden, wenn es vom Fork nicht
überschrieben werden soll.

Der Fork besteht aus 12 Dateien und liegt auf Github:
https://github.com/MaretaDagostino/babelweb-fork

    node_modules/policyfile/lib/server.js
    server.js
    README.md
    AUTHORS   (nur Namen ergänzt)
    LICENSE   (nur Namen ergänzt)
    LICENSE.d3   (nur aktualisiert)
    static/babelweb.html
    static/emergency.html.example  (Beispiel für optionale Zusatzfunktion)
    static/js/babelweb.js
    static/js/d3.js   (nur aktualisiert)
    static/js/d3.min.js   (nur aktualisiert)
    static/css/babelweb.css

Diese Dateien werden ins BabelWeb-Verzeichnis bzw. Unterverzeichnisse wie oben
kopiert. BabelWeb ist meistens dort zu finden (bitte auf dem Rechner prüfen):

    /usr/lib/node_modules/babelweb


Konfiguration (hier für Debian 9 beschrieben)
-------------

Als Voraussetzung für BabelWeb muss der Babel Routing Dienst
auf Port 33123 laufen, BabelWeb ist nur ein Anzeigetool.

Babelweb kann als Dienst gestartet werden, sollte aber mit einem Account ohne
Rootrechte laufen (hier "nobody"). Außerdem wurde das Update-Intervall hier
auf eine Stunde gesetzt, weil das Gezappel der Grafik einfach nur nervt.

    > vi /etc/systemd/system/babelweb.service

    [Unit]
    Description=Fork of BabelWeb
    [Service]
    ExecStart=/usr/bin/node /usr/lib/node_modules/babelweb/server.js port=8080 update=3600000
    User=nobody
    Type=simple
    [Install]
    WantedBy=multi-user.target

    > systemctl enable babelweb
    > systemctl start babelweb.service
    
Kontrolle:

    > systemctl status babelweb.service

Aufruf im Browser zur Kontrolle (Portnummer wie beim Start angegeben):

    > http://<IP-Adresse oder Domain>:8080/babelweb.html

In der Datei "static/babelweb.html" muss in den Zeilen 5 und 8 der Name des
Gateways angepasst werden, dort steht als Platzhalter "##default##".


Extras
------

1) BabelWeb enthält einen einfachen Webserver, der statische Webseiten
   anzeigen kann. Die Einstiegsseite muss "index.htm" oder "index.html"
   heißen. Das Wurzelverzeichnis für Webseiten ist "static".
   Wer keine eigene Einstiegsseite gestalten möchte, kann die Datei
   "static/babelweb.html" entsprechend umbenennen.

2) Systemports < 1024 sind defaultmäßig auf Linux-Rechnern nur Accounts
   mit Root-Rechten zugänglich. BabelWeb sollte aus Sicherheitsgründen
   nicht als Root laufen.
   Es gibt aber eine Möglichkeit, die Systemports und damit auch Port 80
   für ein bestimmtes Programm explizit freizugeben. Benötigt wird das
   Paket "libcap2-bin".

     > apt-get install libcap2-bin

   Dann muss das folgende Kommando bei jedem Systemstart vor dem Start
   von BabelWeb ausgeführt werden:

     > setcap 'cap_net_bind_service=+ep' /usr/bin/node

   Hinweis: Babelweb kann nicht auf Port 80 arbeiten, wenn dort bereits
   ein anderer Webserver (z.B. Apache oder Nginx) läuft.

3) Dieser Fork enthält eine optionale Zusatzfunktion, womit Texte aus dem
   Webbrowser auf den Server übertragen werden können. Die empfangenen
   Texte werden in der Datei "/tmp/babelweb_userdata" abgespeichert
   und können dort von anderen Programmen weiterverarbeitet werden.

   Jeder neu übertragene Text überschreibt den vorhandenen, dabei wird
   nicht zwischen verschiedenen Anwendern unterschieden.

   "static/emergency.html.example" ist ein funktionsfähiges Beispiel,
   wenn die Endung ".example" entfernt wird.

