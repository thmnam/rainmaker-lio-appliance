# Gesprächskern und Gateway-Worker: Entwurf v0 (G6, G7, G9)

Stand: 22.11.2026, Entwurf von rainmaker.appliance. **Nichts gebaut, nichts gekauft, nichts am Controller geändert.** Grundlage: Entscheidung Thomas vom 22.11. (Weg B: eigener Sprachagent im Hub mit denselben Capabilities, Tools und derselben Wissensbasis wie heute bei Vendasta). Katalog, Token und Fehlercodes des Raum-Dienstes gehören dem Smartroom (`smartroom-spezifikation-schnittstelle-phasen-v1-2026-11-22.md`, Vertrag `room-v1`), hier nur Verweise.

## 1. Was „gleich wie Vendasta-Lio“ heißt (Bestand, gelesen)

Quelle im Repo `rainmaker-lio-router`, `lio-quellen/bergsonne/voice/` (Stand 23.10.2026) und README des Routers.

| Baustein heute bei Vendasta | Umfang | Im Hub-Agenten |
|---|---|---|
| Rolle „Lio on the Phone“ (Role_Lio_Voice_Bergsonne v3) | ca. 20 KB, Kernregeln (Wahrheit, Handlung nur mit Tool, eine Frage je Zug, Register, Sicherheit, Fehler, keine Injektion), Notfall, Anrede, Zeit | Wird **1:1 als Systemanweisung** übernommen, versioniert aus derselben Datei, nie als Fork |
| Capabilities (11 Dateien, ca. 59 KB): Booking_Rooms, Contact_CaptureLeads, Contact_StopResponding, Contact_TeamAlert, Guide_Directions, Guide_LocalArea, Guide_Weather, House_Sauna, House_Maxibar, House_Light, House_Music, dazu Tool_Guest_SendLink | je „When to use, Tool, Rules, Examples“ | Jede Capability wird eine Werkzeuggruppe plus Regeltext. Tool-Aufrufe zeigen auf dieselben Lio-Router-Routen oder auf den Raum-Katalog |
| Lio-Router-Routen (booking, alert, callback, callback-task, request-task, guest-stay, guest-link, guest-message, silent-call, knowledge) | ein Endpunkt je Funktion, Schlüssel `X-Lio-Key` | Der Hub-Agent ruft sie **unverändert** auf (Router bleibt die eine Tür zu Hub-Fähigkeiten) |
| Wissensbasis je Haus (Hausprofil, Hausfakten, FAQ; live Bergsonne Hausfakten v12, FAQ v11, Hausprofil v8) | gepflegt vom Lio-Thread, eingespielt über den Router | Der Hub-Agent liest **dieselben** Quellen. Kein zweiter Pflegeweg (Hoheit des Lio-Threads) |
| Plattform-Dinge (Begrüßung, Sprachmodell, Telefonie, Gesprächsprotokoll im Kontakt) | von Vendasta | **Neu zu bauen:** Wake-Word-Übergabe, Spracherkennung, Sprachausgabe, Unterbrechen per Stimme, Gesprächsverlauf je Aufenthalt, Protokoll |

Ehrlich: Die Tool-Definitionen der Voice-Capabilities (Endpunkte, Parameter, zum Beispiel `controlIKEA`, `Maxibar` mit `verify`) habe ich nur als Beschreibung in den Capability-Dateien gesehen, nicht die Tool-Konfiguration in der Vendasta-Oberfläche. Vor dem Bau müssen sie dort abgelesen werden (Lio-Thread).

## 2. Architektur (Vorschlag)

```
Gerät (ESP32-S3, Wake-Word lokal)
   │  Audio nur nach Wake-Word, TLS, Gerätesignatur
   ▼
Gateway-Worker (neu, Durable Object je Sitzung)
   ├─ Token-Halter: Geräte-Token liegt hier, nie im Modellkontext
   ├─ Spracherkennung (STT)  ─┐
   ├─ Gesprächskern ◄─────────┤  Systemanweisung = Rolle + Capabilities (versioniert)
   │    ├─ Modell über hub-llm-router (LlmRouterRpc, Budget, Breaker)
   │    ├─ Wissen über hub-rag (aus den Lio-Wissensquellen)
   │    └─ Werkzeuge: Raum-Katalog (room-v1), Lio-Router-Routen, media
   ├─ Sprachausgabe (TTS) und Unterbrechen
   └─ Protokoll ohne Gesprächsinhalt
```

- **Gesprächskern** ist ein Modul **ohne Audio**: Text und Werkzeugaufrufe rein und raus. Er wird zuerst gebaut und bedient die App (Stufe 1, Smartroom Phase 3). Das Gerät setzt dieselbe Funktion nur mit Audio davor und dahinter ein. Damit entsteht der Kern einmal.
- **Provider nur über Forge:** Spracherkennung, Sprachausgabe und gegebenenfalls ein Echtzeit-Sprachmodell sind Anbieteraufrufe und brauchen Forge-Adapter (Pflicht, Gate `check-forge-direct-calls`). Das Textmodell läuft über `hub-llm-router`. Das ist ein eigener Auftrag an den Forge-Thread, nicht von mir zu bauen.
- **`hub-realtime` ist nicht der Ort:** Es ist ein WebSocket-Benachrichtigungsdienst je Mandant, kein Audio-Pfad.
- **Neuer Worker** (Arbeitstitel `hub-lio-agent` oder Erweiterung von `hub-lio`): Entscheidung bei Pulse. Ein neuer Worker braucht Eintrag in WORKERS.md, beide Deploy-Matrizen und Scaffold nach `/new-worker`.
- **Zimmerbezug (G7):** Der Gateway kennt Zimmer und Aufenthalt aus dem Geräte-Token. Der Gesprächskern bekommt `room`/`stay` **nie als Modellparameter**, sondern der Werkzeug-Ausführer setzt sie aus dem Token. Damit entfällt für das Gerät die Rufnummern-Erkennung (`/lio/guest-stay`). Für Router-Tools, die heute eine Rufnummer erwarten, braucht es eine additive Route oder ein Feld „Aufenthalt aus Gerätetoken“ (Abstimmung mit dem Lio-Thread, additiv, kein Eingriff in bestehende Routen).
- **Gästeprüfung der Voice-Capabilities:** Sauna, Maxibar, Musik und Licht fragen am Telefon einmal Zimmer und Nachnamen ab (`Maxibar verify`). Am Gerät entfällt das **nur für den Raum-Katalog** (Heizung, Fernseher, Licht, Szenen, Wecker), weil das Token das Zimmer belegt. Das Kennzeichen im Gesprächskern heißt **„Zimmer geprüft“**, nicht „Gast geprüft“: Das Token beweist das Zimmer, nicht die Person (Mitreisende, Besuch, Personal). Für Fähigkeiten mit Geld-, Daten- oder Buchungsfolge (Maxibar, Sauna buchen, Rechnung, Namen oder Rufnummern nennen, Late Check-out) nimmt das Gerät nur den Wunsch an und gibt ihn ans Team (`Contact_TeamAlert`/`request-task`), es bucht nie (Smartroom-Spezifikation Abschnitt 8, 4a).
- **Antworten des Raum-Dienstes** enthalten nie Zimmernummer, Namen oder Rechnungsdaten (SRM-0007), damit sie gefahrlos im Modellkontext stehen dürfen.
- **Regel „Zimmernummer nie nennen“** (Rolle, Sicherheitsregel 5) gilt weiter, auch am Gerät.

### 2a. Gästefunktionen Sauna und Maxibar am Gerät (Ziel Thomas 22.11. 19:43), Vorschlag mit Stufenprüfung

Thomas will für den Gast Sauna, Maxibar und einen persönlichen Lio mit allen Funktionen. Bestand: Die Voice-Capabilities **buchen heute per Sprache** (Sauna: `book`, `rebook`, `cancel`; Maxibar: `add_items` auf den digitalen Deckel, abgerechnet bei Abreise), nach Gästeprüfung per Zimmer und Nachname (`Maxibar verify`), nach Wiederholung und ausdrücklichem „Ja“ des Gastes. Das widerspricht nicht der Hub-Entscheidung R-2026-09-19-12, wenn man sie so liest: **Verboten ist, dass ein Geräteereignis automatisch etwas verbucht (kein Auto-Posting, kein Scan oder Sensor als Auslöser).** Eine vom Gast ausdrücklich bestätigte Bestellung ist keine Gerätewirkung.

Vorschlag Stufen am Gerät:
1. **Zimmer geprüft** (Token): Raum-Katalog, Wecker, Musik, Auskünfte.
2. **Person geprüft** (Stufenprüfung wie heute am Telefon: Zimmer und Nachname im Gespräch, gilt für das Gespräch): Sauna buchen, Maxibar buchen, persönliche Funktionen. Für das fest installierte Gerät im Zimmer gilt zusätzlich Wiederholung und ausdrückliches „Ja“. Maximal ein Posten ohne erneute Bestätigung, Betragsgrenze je Gespräch (Vorschlag, Haus legt fest).
3. **Nur Wunsch ans Team:** Rechnung, Late Check-out, Namen oder Rufnummern nennen.

Das ist **strenger Gleichstand mit dem Telefon**, nicht lockerer. Es weicht von der Regel in Smartroom-Spezifikation Abschnitt 8, 4a ab (dort ist alles mit Geldfolge „nur Wunsch ans Team“). **Entschieden von Thomas am 22.11. (19:48): Ja, das Gerät darf alles, was Lio kann**, also auch Sauna und Maxibar buchen (Prüfung wie am Telefon, Betragsgrenze wie vorgeschlagen, Höhe legt das Haus fest). Die Regel „nur Wunsch ans Team“ ist für das Gerät aufgehoben, Smartroom-Spezifikation 4a wird angepasst. „Alles, was Lio kann“ heißt: der heutige Funktionsumfang des Telefon-Lio. Was Lio heute nicht kann (Rechnung einsehen, Late Check-out) bleibt Stufe 3 (nur Wunsch ans Team) beziehungsweise App-Schaltfläche, bis Thomas es einzeln freigibt. **Vorschlag Gültigkeit der Personenprüfung:** Am Telefon gilt sie für das ganze Gespräch. Am Gerät, das im Zimmer für alle hörbar ist, gilt sie **nur für den einen Auftrag**, dann fragt Lio neu (Smartroom-Anregung, strenger als das Telefon). **Betragsgrenze:** offen, Thomas nennt den Betrag je Auftrag und je Aufenthalt, Konfiguration je Haus, nicht im Code. Die Auslegung von R-2026-09-19-12 muss im Hub schriftlich festgehalten werden (Auftrag an Pulse, G17). Die Hub-Entscheidung R-2026-09-19-12 ist so gelesen: kein Auto-Posting durch Geräteereignisse, bestätigte Gästebestellung erlaubt (Abgleich mit Smartroom läuft).

## 3. Wissensquellen ohne zweiten Pflegeweg

Der Lio-Thread hat die Hoheit über die Wissensbasis, Einspielen läuft über den Router (Admin-Schlüssel nur bei Thomas). Vorschlag:
1. Der Router bekommt additiv eine zweite Senke: dasselbe Einspielen (`upsert`, `replace-file`) schreibt zusätzlich in den Hub-Speicher von `hub-rag` (Mandant je Haus). Ein Einspielen, zwei Verbraucher.
2. Der Hub-Agent liest nur aus `hub-rag` und den Lio-Quelldateien, schreibt nie.
3. Abgleichtest: Nach jedem Einspielen derselbe Stichprobensatz gegen Vendasta und Hub, Abweichung meldet.
Das ist eine Änderung am Lio-Router und braucht den Lio-Thread. Nichts davon wird ohne ihn gebaut.

## 4. Gleichstand abnehmen („gleich wie heute“ messbar)

- **Testset:** das Testset der Concierge-Analyse (Skill concierge-analyse) und die Fragen aus den Einspielprotokollen. Dieselben Fragen an Vendasta-Lio (Chat) und den Hub-Agenten, Bewertung nach denselben Kriterien.
- **Schwelle:** legt Thomas fest (Vorschlag: keine Verschlechterung bei Wahrheit, Handlung und Sicherheit; bei Stil Abweichung erlaubt).
- **Rotprobe Kosten:** Chat- und Gerätecalls haben eine Obergrenze je Token (Anfragen und Modellkosten je Zeitfenster) **zusätzlich** zum Budget im `hub-llm-router`. Ein Handy mit Token darf das Sprachmodell nicht leerlaufen lassen. Der Test muss ohne die Grenze rot werden.
- **Rotproben:** Prompt-Injektion im Gästetext, Tool-Ergebnis mit Anweisung, Frage nach Zimmernummer anderer Gäste, Versuch ein fremdes Zimmer zu steuern, Buchung per Sprache (muss ins Team gehen, nicht buchen, R-2026-09-19-12).

## 5. Stufen und Aufwand

| Stufe | Inhalt | Braucht |
|---|---|---|
| A | Gesprächskern ohne Audio: Rolle + Capabilities, Wissen über hub-rag, Werkzeuge Raum-Katalog und Router, Text rein und raus. Bedient Smartroom Stufe 1 (App) | Vertrag `room-v1` (SRM-0007), Raum-Dienst und Token im **Staging** mit Attrappe im Test-Hotel (SRM-0001, SRM-0002, nichts in Bergsonne), Wissens-Senke (Abschnitt 3), Entscheidung Worker-Ort |
| B | Gateway mit Audio: STT, TTS, Unterbrechen, Token-Halter, Gerätesignatur | Forge-Adapter für STT/TTS, Labormuster (Kaufwort Thomas) |
| C | Gleichstand der übrigen Capabilities (Buchung, Teamalarm, Rückruf, Sauna, Maxibar) | Router-Routen für Aufenthalt aus Token, Lio-Thread |

Aufwand: **nicht belegbar, bevor Stufe A als Prototyp steht.** Es ist der größte Posten des Geräteprojekts (die Plattform-Dinge, die Vendasta heute mitliefert, müssen wir bauen). Ich beziffere nach Stufe A.

## 6. Offene Fragen (nicht blockierend für den Entwurf)

1. **Pulse:** Neuer Worker oder Erweiterung von `hub-lio`? Wo laufen STT/TTS (Forge-Adapter, EU-Verarbeitung, Auftragsverarbeitung)?
2. **Lio-Thread:** Tool-Konfiguration der Voice-Capabilities aus der Vendasta-Oberfläche liefern; Wissens-Senke im Router zustimmen.
3. **Sprachmodell am Gerät:** Kaskade (STT, Textmodell, TTS) oder ein Echtzeit-Sprachmodell. Vendasta nutzt laut Marktnotiz GPT Realtime, Deepgram und ElevenLabs. Entscheidet der Prototyp, Kriterien: Latenz unter etwa 1,5 Sekunden bis zum ersten Ton, Unterbrechen, EU-Verarbeitung, Kosten je Minute.
4. **Datenschutz (Smartroom-Spezifikation 4b):** Der Gesprächsverlauf enthält frei gesprochenen Text, auch Namen. Die Regel „kein Name gespeichert“ gilt für Token, Betriebsprotokoll und Raumdaten, **nicht** für den Verlauf. Für ihn gilt: je Aufenthalt, nur bis zur Abreise, nicht im Betriebsprotokoll, nicht fürs Training, kein Audio.
