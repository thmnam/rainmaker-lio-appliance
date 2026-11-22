# rainmaker.appliance: Stand, Idee, Ziel (für den Smartroom-Thread)

Stand: 22.11.2026 19:55, Thread rainmaker.appliance. Ausführlich: `/mnt/project-files/appliance/abgleich-und-schnittstelle-2026-11-22.md` (Repo `thmnam/rainmaker-lio-appliance`, Entwurf-PR #2) und `docs/entscheidungsvorlage/` im Repo.

## Idee
Ein offenes Sprachgerät fürs Gästezimmer, das nur Lio kann: Gast sagt „Weck mich um 7“, „Fernseher lauter“, „Was gibt es zum Frühstück?“. Es ersetzt Alexa, Google Home und HomePod, sendet nur nach dem Wake-Word Audio, hat einen Hardware-Stummschalter, keine Kamera, Holzgehäuse, gutes Mikrofonfeld und echten Klang. Musik nur aus den Bergsonne-Playlists des Haus-Controllers. Das Gerät ist **ausleihbar** (Empfang gibt es aus, Kopplung an Zimmer und Aufenthalt, bei Rückgabe Reset). Ein Geräteereignis erzeugt nie eine Buchung (R-2026-09-19-12).

## Ziel
1. Eigenbau (Variante D, ESP32-S3 mit XMOS-Mikrofonfeld), Sprachagent im Hub (EU), der dieselben Lio-Router-Werkzeuge und denselben Raum-Katalog nutzt wie App und Gespräch.
2. Pilot Bergsonne mit drei bis fünf Zimmern, danach Kleinserie, nur mit Freigabe von Thomas je Phase.
3. Kein neues Parallelsystem: Gerät ist ein weiterer Client des Hubs (Gateway-Worker, Service Bindings), nutzt den Raum-Dienst und den Token-Prüfpfad des Smartrooms.

## Stand
- **Nur Papier.** Entscheidungsvorlage, Marktnotizen, Vendasta-Anfrage und Umweg über Automationen (ungetestet), loratech-Fragenkatalog (niemand kontaktiert), Testplan Phase 0 (Entwurf).
- Keine Hardware, keine Firmware, kein Gateway-Code, keine Termine. Kostenrahmen 150 bis 350 Euro je Gerät ist eine Annahme.
- Vendasta bietet keine Schnittstelle für eigene Geräte (Antwort 16.11.). Darum Weg B (eigener Hub-Agent), von Thomas noch nicht bestätigt.
- Karte B-27 (Nachfragetest NFC/QR) steht auf `proposed`, hub-atlas hat kein Binding auf hub-iot.
- CE und Funk: nicht im Plan, jetzt als Punkt aufgenommen (Rechtsfrage, nicht geprüft).
- Abgestimmt mit smartroom: Katalog und Token-Regeln gehören dem Smartroom, IR Controller bleibt fest im Zimmer, Voice nicht offlinefähig, Zimmer nur aus Token, Antworten zu Q1 (Ausleihe), Q2 (Geräte-Katalog `media`) und Q5 (Scan-Ereignis) im Abgleichsdokument, Abschnitt 6.

## Nächste 24 Stunden (ohne Hardware, ohne Kauf, ohne Eingriff beim Controller)
1. **G4:** Scan-Ereignis in den B-27-Testplan im Repo einarbeiten (`docs/phase-0/b27-testplan.md`), Smartroom liest gegen.
2. **G8/G16:** Ausleih-Ablauf als Anforderung für die Cockpit-Oberfläche in eine eigene Datei im Repo, Smartroom ergänzt die Staff-Seite.
3. **G14:** Geräte-Katalog `media` (Musik) und Hilfetaste als Entwurf (Namen, Fehlercodes, Token wie Raum-Katalog), Zustellung der Playlists als offene Frage an den Controller (nur lesen, nichts ändern).
4. **G5:** Entwurf der CE- und Funk-Klärung (Fragen an eine Prüfstelle oder einen Fachanwalt), nur als Text, nichts gesendet.
5. **G6/G7/G9:** Entwurf Gesprächskern und Gateway-Architektur **nur**, wenn Thomas Weg B bestätigt. Sonst stehen sie still.
6. Spezifikation v1.2 des Smartrooms gegenlesen und Fundstellen direkt melden.

## Was mich blockiert (alles bei Thomas)
- **G3 Weg B** (eigener Sprachagent im Hub statt Vendasta-Telefonweg): blockiert Gesprächskern, Zimmerbezug für Lio, Gateway.
- **G2** Gerät ausleihbar oder fest: wirkt auf Token-Ausgabe und IR.
- **G5** Ja zur CE-Klärung mit externer Stelle.
- **G10** Kaufwort für Laborgeräte (bis etwa 500 Euro) und Ergebnis von B-27. Ohne beides kein Labor.
- Zugriff auf das Controller-Repo `bergsonne-smartbuilding` aus diesem Thread: keiner, nur nach Thomas' Wort.

## Nachtrag 22.11. 19:43: Ziele von Thomas
- **Für den Gast:** Sauna, Maxibar, persönlicher Lio mit allen Funktionen. Ziel 1 Gästefreude.
- **Fürs Haus:** Alles aus, wenn niemand im Zimmer; Heizung aus bei offenem Fenster; Wasserschaden und Rauchmelder (Smartroom-Konzept 15 und 16.4). Ziel 2 perfekter Betrieb. Das Gerät wirkt dabei nur als Ansage im Zimmer mit, nie als Melder, und hört nicht auf Anwesenheit (kein Audio vor dem Wake-Word).
- **Aufwand Pilot:** 100 bis 300 Euro je Zimmer. Meine Annahme für das Gerät allein liegt bei 150 bis 350 Euro, ohne Ventil und Sensoren. Offen, ob das Gerät in diesem Rahmen liegt.
- **Voice braucht Internet:** als Hinweis in den Grenzen entfernt (Lesart des Koordinators). Technisch bleibt es wahr, solange Sprache zu Text im Hub läuft.
- Sauna und Maxibar per Sprache: Vorschlag Stufenprüfung in `docs/gateway/gespraechskern-und-gateway-entwurf-v0.md`, Abschnitt 2a, Entscheidung Thomas steht aus.
- **Entscheidung 22.11. 19:48:** Das Gerät darf alles, was Lio kann (Sauna und Maxibar buchen, Prüfung wie am Telefon, Betragsgrenze). Das Lio-Gerät wird **separat** zum Zimmerrahmen von 100 bis 300 Euro geführt.
