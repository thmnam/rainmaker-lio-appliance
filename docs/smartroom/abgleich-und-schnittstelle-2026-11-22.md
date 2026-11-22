# Appliance und rainmaker.smartroom: Abgleich und Schnittstelle (Entwurf v0)

Stand: 22.11.2026. Entwurf, nichts gekauft, nichts am Controller (`thmnam/bergsonne-smartbuilding`) geändert, nichts bestellt. Gegenstand: Konzept `konzept-zimmersteuerung-tv-heizung-pilot-2026-11-22.md` (Abschnitte 4.4, 10, 10.2, 11.1, 12.1a, 12.5, 12.6.5) gegen den Stand dieses Repos.

## 1. Heutiger Stand der Appliance (Antwort auf 12.6.5)

| Thema | Stand | Beleg |
|---|---|---|
| Entscheidung | Variante D (Eigenbau ESP32-S3, XMOS-Mikrofonfeld, 2.1-Lautsprecher, Holzgehäuse) freigegeben 13.11. Offen blieben laut STAND.md vom 13.11.: Lio-Weg B (eigener Sprachagent im Hub), B-27 zuerst, loratech-Gespräch, Pilot Bergsonne 3 bis 5 Zimmer. Eine Entscheidung dazu steht nicht im Repo. | STAND.md |
| Hardware | **Nur Papier.** Zielspezifikation (ESP32-S3, 16 MB Flash, XVF3800 oder ES7210 mit Echo-Referenz, PoE+ oder USB-C PD 12 V, Hardware-Stummschalter, LED, Hilfetaste, NFC im Sockel), kein Schaltplan, keine Stückliste, kein Labormuster bestellt. Kostenrahmen 150 bis 350 Euro je Gerät ist eine Annahme. | docs/entscheidungsvorlage |
| Software | **Keine.** Weder Firmware noch Gateway-Code. Geplant: Wake-Word auf dem Gerät, danach Audio an einen Gateway-Worker im Hub, dort Sprache zu Text, Modell über hub-llm-router, Text zu Sprache, Aufruf der Lio-Router-Werkzeuge. | STAND.md, Kap. 3 und 5 |
| Zeitplan | Phasen 0 bis 3 ohne Termine (0 Nachfragetest B-27, 1 Labor, 2 Pilot 3 bis 5 Zimmer, 3 Gehäuse und Kleinserie). Jede Phase endet mit Freigabe von Thomas. Karte B-27 im Hub steht auf `proposed`. | Kap. 6, Hub-Karte B-27 |
| Vendasta | Keine Schnittstelle für eigene Geräte (Antwort 16.11.). Umweg über Automationen ist dokumentiert, nicht getestet. Darum Weg B. | docs/vendasta |
| Mobil | Gerät ist **ausleihbar**, an Zimmer und Aufenthalt per NFC oder QR gekoppelt, Gedächtnis gehört dem Aufenthalt. | Kap. 1, 5.1 |
| Musik | Nur Bergsonne-Playlists vom Haus-Controller, Zustellung offen. | STAND.md |
| IR Controller | **Im Appliance-Entwurf nicht vorgesehen.** Kein Platz, keine Sichtlinie, keine Stromauslegung dafür. | – |
| CE | **Im Plan nicht enthalten.** Nur als Frage an loratech (Nr. 17) und als „Datenschutzprüfung“ in Phase 3. | Fragenkatalog |

## 2. Abgleich mit dem Smartroom-Konzept

| Nr. | Konzept sagt | Appliance sagt | Folge |
|---|---|---|---|
| A1 | Stufe 3 und 10.2: „Spracherkennung im Gerät“, „ohne Umweg über Pi und Internet“, Phase 5 „Sprachbefehle lokal“ | Nur das Wake-Word läuft auf dem Gerät. Sprache zu Text, Modell und Antwort laufen im Hub (EU). Ohne Internet gibt es keine Sprachsteuerung. | Konzept anpassen: Voice ist **nicht** offlinefähig. Offline-Rückfall bleiben Wandschalter, Ventil am Heizkörper, Fernbedienung an der Team (so schon Abschnitt 16.1). Ob ein kleiner lokaler Befehlssatz auf dem ESP32-S3 (deutsch) taugt, ist **ungeprüft**. |
| A2 | 10.3 und 11.1: Beim Lio-Gerät ist das Zimmer „fest hinterlegt“ | Gerät ist mobil, Zimmerbindung entsteht bei Ausleihe und endet bei Rückgabe oder Abreise | Zimmerbindung des Geräts ist ein **Token mit Ablauf**, kein fester Eintrag (Abschnitt 3.3). |
| A3 | 10.2: IR Controller als Baugruppe ins Gerät, Sichtlinie zum Fernseher | Gerät steht dort, wo der Gast es hinstellt, und kann in die Hand genommen werden. Ein eingebauter IR Controller zeigt dann irgendwohin, und das Bluetooth zum Ventil hängt am Standort. | **Empfehlung: IR Controller nicht ins Gerät einbauen.** Er bleibt fest im Zimmer (Sichtlinie zum Fernseher, Ventil in Reichweite). Das Gerät spricht über den Raum-Dienst mit ihm. Das löst Sichtlinie, Standort und CE zugleich (Abschnitt 4). |
| A4 | 11.1: Namensprüfung gegen frontdesk für Gäste-Handys | Gerät braucht keine Namensprüfung, aber ein Token, das Personal bei Ausleihe ausgibt | Zwei Token-Arten, ein Prüfpfad (Abschnitt 3.3). |
| A5 | 11.4 und 12.4: Late Check-out per Button bucht 25 Euro auf das Folio | Hub-Entscheidung R-2026-09-19-12: Ein Geräteereignis erzeugt **nie** eine Verpflichtung (Rechnungsposten) | Buchen darf nur die App-Schaltfläche mit ausdrücklicher Bestätigung des Gastes. Per Sprache am Gerät nimmt Lio den Wunsch nur an und gibt ihn an das Team. Muss in die Regeln des Konzepts. |
| A6 | 12.5 Phase 0: „Appliance-Stand aus dem anderen Projekt“ | Hier geliefert (Abschnitt 1) | Phase 0 Punkt erledigt. |
| A7 | 11.1: QR-Schild je Zimmer | B-27 testet gerade NFC oder QR im Zimmer als Stellvertreter für Nachfrage (Scans pro Aufenthalt) | Das QR-Schild **ist** der B-27-Test. Beide Projekte sollten dasselbe Ereignis (Scan, Aufenthalt, kein Name, keine Buchung) zählen, nicht zwei Zähler bauen. |

## 3. Schnittstelle zum Raum-Dienst (Vorschlag, mit rainmaker.smartroom abzustimmen)

Grundsatz aus Konzept 10.1 und Charter 3.11: **Eine** Raum-Schnittstelle, die Gespräch (Lio), App und Gerät gleich nutzen. Gerät zu Hub über einen Gateway-Worker, Worker untereinander über Service Bindings, Agenten über MCP. Der Pi bleibt Ausführender im Haus.

### 3.1 Bestand im Hub (gelesen, nichts aufgerufen)

- `hub-smartcontrol` hat schon `POST /smartcontrol/{businessId}/room/{room}/action` mit Körper `{system: 'radio'|'automation'|'lights', action, params}` und leitet an die Haus-Controller weiter. Das ist der natürliche Platz für `heat`, `tv`, `light`, `scene` als neue Aktionen (Hub-Seite als Fassade, Pi als Ausführung). Der Zimmerparameter kommt dort aus dem Pfad, **nicht** aus einem Token.
- `hub-iot` ist Proxy ohne öffentliche Route, einziger Aufrufer `hub-rules` (Karte B-27: Binding von hub-atlas fehlt noch).
- `hub-lio` hat `POST /lio/{businessId}/verify-guest` (Zimmer plus Name). Der Namensvergleich dort (Pfad über den Zimmer-Cache, den Datenbankpfad habe ich nicht geprüft) arbeitet mit `includes` auf dem kleingeschriebenen Namen (ein Buchstabe trifft jeden Gast mit diesem Buchstaben), Turnstile ist optional (Karte SEC-0038). **Für den Zimmertoken so nicht übernehmen**: normalisierter Vergleich (Müller gleich Mueller), Gleichheit statt Teilstring, Fehlversuchsgrenze, Pflicht-Turnstile.
- Der Lio-Router erkennt Gäste heute über die Rufnummer (`/lio/guest-stay`). Für Gerät und App fehlt eine additive Route, die Zimmer und Aufenthalt aus dem Token auflöst.

### 3.2 Aktionen (Katalog, Whitelist)

Jeder Aufruf trägt `requestId` (Idempotenz) und liefert `{ok, state, assumed, requestId}`. `assumed: true` heißt: Zustand ist angenommen (Infrarot ohne Rückmeldung), nicht gemessen.

| Aktion | Eingabe | Regeln |
|---|---|---|
| `heat.set` | `target` in Grad | Grenzen 16 bis 24 (Konzept 10.1, Haus bestätigt noch), nur Stay-Status `InHouse`, Plan wirkt wieder ab Abreisetag 10:00 |
| `heat.get` | – | Ist und Soll vom Ventil, echt |
| `tv.cmd` | `cmd` aus Liste: `power_on`, `power_off`, `vol_up`, `vol_down`, `mute`, `prog_up`, `prog_down`, `prog_direct` (mit `value`), `voiceover` | Whitelist, Wiederholsperre, `assumed: true` solange keine messende Steckdose |
| `light.set` | `lightId`, `on`, optional `level`, `colorTemp` | nur Leuchten, die das Haus je Zimmer freigegeben hat |
| `scene.run` | `scene`: `arrive`, `goodnight`, `leave`, `return`, `read`, `relax`, `film`, `night` | Szenen setzt das Haus, Gäste wählen nur aus |
| `state.get` | – | Gesamtzustand des eigenen Zimmers |

Fehlercodes (feste Liste): `ROOM_NOT_ACTIVE`, `TOKEN_INVALID`, `OUT_OF_RANGE`, `UNKNOWN_CMD`, `RATE_LIMITED`, `DEVICE_UNREACHABLE`, `DENIED_BY_SWITCH`.

### 3.3 Zimmertoken und Geräte-Token

Der Raum-Dienst nimmt **nie** ein Zimmer als freien Parameter von Lio, App oder Gerät an. Das Zimmer steht im geprüften Token und wird serverseitig gelesen. Ein vom Modell gelieferter Zimmerwert wäre per Prompt-Injection fälschbar.

| Feld | Gast-Handy (Konzept 11.1) | Lio-Gerät |
|---|---|---|
| `kind` | `guest-session` | `appliance` |
| Ausgabe | nach QR-Scan und einmaliger Namensprüfung (Server gegen frontdesk über Forge) | Personal bei Ausleihe per NFC oder QR, keine Namensprüfung |
| Inhalt | `biz`, `room`, `stay`, `exp`, Geräte-ID des Handys | `biz`, `room`, `stay`, `exp`, Geräte-ID (Seriennummer) |
| Ablauf | Abreise plus Karenz | Rückgabe oder Abreise, je nachdem was zuerst kommt |
| Entzug | Zimmerwechsel (neue Bindung nach Rückfrage), Abreise, Sperre durch das Team | zusätzlich bei Rückgabe: Gerät setzt Gedächtnis zurück |
| Speicherung | Zimmer, Geräte-ID, Ablauf, kein Name | dasselbe |

Prüfpfad: Gateway-Worker prüft Signatur und Ablauf, fragt bei jedem steuernden Aufruf den frontdesk-Status des Aufenthalts (gecacht, kurze Frist), ruft den Raum-Dienst per Service Binding. Lesen aus frontdesk läuft über Forge (`hub-frontdesk`), nicht direkt. Alle Aufrufe an Vendasta, frontdesk und Google ausschließlich über Forge.

Offen: Wie kommt der Zimmertoken in einen Aufruf von Vendasta-Lio (Gespräch, Stufe 1)? Das Werkzeug in Vendasta erhält nur AG-ID und die Parameter, die das Modell einsetzt. Ein Token im Gesprächskontext ist nur so geheim wie der Kontext. Vorschlag: Stufe 1 läuft zuerst über die Lio-App (Token im App-Header, Lio-Werkzeug bekommt eine kurzlebige Gesprächs-ID statt des Tokens); am Gerät (Weg B) liegt der Token im Gateway und nie beim Modell.

### 3.4 Verbindliche Regeln für alle drei Wege

1. Wandschalter und Ventil von Hand gewinnen immer (Konzept 16.1). Fehler `DENIED_BY_SWITCH`, wenn ein Aufruf einer Handbedienung widerspräche.
2. Kein Gerätebefehl erzeugt eine Buchung oder einen Rechnungsposten (R-2026-09-19-12). Buchbare Dinge (Late Check-out, Minibar) gehören nicht in diese Schnittstelle.
3. Kein Audio vor dem Wake-Word, keine Gästenamen im Raum-Dienst, Protokoll ohne Gesprächsinhalt.
4. Kein neues Geheimnis auf dem Pi oder im Repo, Schlüssel nur über `rmsec`.

### 3.5 Abgestimmt mit rainmaker.smartroom (Spezifikation v1.1, 22.11.)

Die Spezifikation `/mnt/project-files/smartroom/smartroom-spezifikation-schnittstelle-phasen-v1-2026-11-22.md` führt diesen Entwurf fort und gilt bei Abweichungen. Änderungen gegenüber Abschnitt 3:
1. Das Zimmer steht auch **nicht im Pfad**: `POST /room/act`, `GET /room/state`. Ein Zimmer nennen nur Planjob und Staff. Die heutige Route von `hub-smartcontrol` (`room/{room}/action`) taugt für Gast und Gerät so nicht.
2. Der rohe `POST …/proxy` in `hub-smartcontrol` ist nur für Staff. Gast, App und Gerät erreichen ihn nie. `hub-smartcontrol` hängt heute hinter `hub-cockpit-api` (Sitzung), ein Gastzugang braucht eine eigene öffentliche Tür davor, mit Pflicht-Turnstile an der Token-Tür.
3. Statusprüfung gegen frontdesk bei jedem steuernden Aufruf, Zwischenspeicher höchstens 5 Minuten (statt „kurze Frist“).
4. `PUT /room/{room}/plan` gehört weder zum Gast- noch zum Gerätekatalog.
5. Gateway-Worker ruft den Raum-Dienst mit dem Geräte-Token. Das Modell sieht ihn nie. **Ohne Verbindung zum Hub steuert das Gerät nichts.**
6. Zusätzliche Fehlercodes: `WINDOW_OPEN`, `TOKEN_EXPIRED`, `NOT_ALLOWED_FOR_GUEST`.

Offen für Thomas (vor Phase 3): Gilt R-2026-09-19-12 so, dass der App-Button mit Bestätigung buchen darf und das Gerät nie?

## 4. Platz für den Shelly IR Controller und CE

**Platz und Sichtlinie.** Maße, IR-Sendewinkel und Leistung des IR Controllers (S4CC-0D1XX1WW1) sind uns **nicht belegt** (nur das Handbuchfoto: USB-C 5 V 1 A, Reichweite bis 5 m zum Empfänger). Ein Einbau bräuchte ein Infrarotfenster im Holzgehäuse, ein Gehäuse, das IR und Bluetooth durchlässt, eine 5-V-Schiene aus PoE oder PD und freie Sicht zum Fernseher. Weil das Gerät mobil ist, ist das nicht zu garantieren.

**Empfehlung: getrennt lassen.** Der IR Controller bleibt ein eigenes, fest platziertes Teil im Zimmer (Sichtlinie zum Fernseher, Ventil in Bluetooth-Reichweite). Das Gerät greift über den Raum-Dienst darauf zu. Damit fällt zugleich der Einbau einer fremden Funkplatine weg.

**CE-Frage (Rechtsfrage, nicht geprüft, hier nur meine Einordnung).**
- Schon das Appliance-Gerät selbst (ESP32-S3 mit WLAN und Bluetooth, Netzteil, Verstärker) ist Funkanlage. Wer es als Hersteller in Verkehr bringt oder in mehreren Häusern einsetzt, braucht eine eigene Konformitätserklärung (Funkanlagenrichtlinie, EMV, Sicherheit), dazu Pflichten wie ElektroG-Registrierung. Vorzertifizierte Module (zum Beispiel ESP32-S3-WROOM) und fertige Boards verringern den Prüfaufwand, nehmen die Herstellerpflicht aber nicht ab.
- Das steht heute **nicht** im Plan. Phase 3 (Kleinserie) nennt nur Datenschutz. Vorschlag: CE und Funk als eigenen Punkt in Phase 2 aufnehmen, vor dem Pilot im Gästezimmer mit einer Prüfstelle oder Fachanwalt klären, ob und wie ein Pilot im eigenen Hotel zulässig ist.
- Ein eingebauter IR Controller würde zusätzlich eine zertifizierte Fremdbaugruppe in ein anderes Gehäuse stecken. Das ändert Abstrahlung und Zulassungslage und ist der teurere Weg.

## 5. Vorschlag zur Aufteilung der Arbeit

| Baustein | Wohin | Wer |
|---|---|---|
| Fachliche Spezifikation, Regeln, Pilotabnahme im Haus | rainmaker.smartroom | smartroom-Thread |
| Raum-Dienst (Hub-Fassade, Token-Prüfung, Katalog) | Hub, voraussichtlich Erweiterung von `hub-smartcontrol` | pulse (eigener Thread, Karte) |
| Pi-Ausführung (`heat`, `tv`, `light`, `scene`, Plan lokal) | `bergsonne-smartbuilding` | erst nach ausdrücklichem Wort von Thomas |
| Gateway-Worker für Gerät (Audio, Token, Sprachagent) | Hub | rainmaker.appliance (dieses Projekt), erst nach Freigabe Phase 1 |
| Firmware, Hardware, CE | dieses Repo | rainmaker.appliance, Labor erst nach Freigabe |
| QR-Schild und B-27-Ereignis | Hub (hub-atlas, hub-iot) | gemeinsam, ein Zähler |

Die Phasen 1 bis 4 des Konzepts brauchen die Appliance **nicht**. Sie hängt erst an Phase 5. Darum bleibt die Appliance ein eigenes Projekt mit eigener Freigabe, nutzt aber ab Phase 2 den Raum-Dienst des Konzepts. Ein einziger gemeinsamer Thread würde die Appliance an den Pilot im Zimmer 13 koppeln, den sie nicht braucht.

## 6. Antworten an rainmaker.smartroom (Q1, Q2, Q5), Entwurf

### Q1 Ausleihe, Rückgabe und Entzug des Geräte-Tokens (Ablauf als Text)

1. **Voraussetzung:** Es gilt dasselbe Gastzugangsfenster wie für den Gast-Token (Check-in-Zeit oder Status `InHouse`, Smartroom-Spezifikation 3.1). Das Gerät wird nur auf Wunsch des Gastes ausgegeben, nie automatisch.
2. **Ausgabe durch das Team:** Personal meldet sich im Cockpit an (Staff-Anmeldung, nicht über den Gästetoken) und öffnet „Gerät ausgeben“. Es wählt den Aufenthalt aus der Liste der aktuellen Aufenthalte (aus frontdesk über Forge, die Liste zeigt Zimmer und Aufenthalt, keine Gästenamen, keine freie Eingabe) und das Gerät (NFC-Tag im Sockel oder QR am Sockel liefert die Geräte-ID).
3. **Kopplung:** Das frisch eingeschaltete Gerät fragt beim Gateway einen einmaligen Koppelcode an (sechs Stellen, fünf Minuten gültig). Personal bestätigt ihn im Cockpit. Erst dann stellt der Server das Geräte-Token aus (`kind: appliance`, `biz`, `room`, `stay`, `deviceId`, `exp`) und übergibt es dem Gerät über die gesicherte Verbindung. Das Token steht nie auf Papier, nie in einem QR und nie im Gesprächskontext des Modells. LED „gebunden“.
4. **Laufzeit:** Ablauf spätestens am Abreisetag plus Karenz wie beim Gast-Token. Zusätzlich kurze Erneuerung (zum Beispiel 24 Stunden), damit ein Entzug auch bei einem Gerät greift, das zwischendurch offline war. Unabhängig davon prüft der Raum-Dienst bei jedem steuernden Aufruf den Aufenthaltsstatus (Zwischenspeicher höchstens 5 Minuten).
5. **Rückgabe:** Personal scannt das Gerät am Sockel und wählt „Zurück“. Der Server entzieht das Token und löscht das Gedächtnis des Aufenthalts. Das Gerät setzt sich beim nächsten Kontakt zurück (Token, WLAN-Zuordnung des Aufenthalts, Gesprächsverlauf).
6. **Automatischer Entzug:** Abreise, Stornierung, Zimmerwechsel (Rückfrage an den Gast, neue Bindung erst nach Bestätigung) oder „Gerät verloren“ durch Personal (sofortige Sperre).
7. **Staff-Oberfläche:** im Cockpit (Gerätebestand, wer hat was, Ausgabe, Rückgabe, Sperre, Batterie- und Erreichbarkeitsstand). Kein Zimmer wird je am Gerät eingestellt.
8. **Offen fürs Haus:** Wer vom Team gibt aus (das Haus hat keine Team), braucht es eine Kaution oder einen Vermerk im Folio (Achtung R-2026-09-19-12: nur Hinweis, keine automatische Buchung durch das Gerät).

### Q2 Reicht der Raum-Katalog 1:1?

Für Heizung, Fernseher, Licht und Szenen: **ja, 1:1**, ohne Sonderfall am Gerät. Das Gerät braucht darüber hinaus drei Dinge, die nicht in den Raum-Katalog gehören:
- **Musik** (`media.play`, `media.stop`, `media.volume`, nur Bergsonne-Playlists vom Controller): eigener, kleiner Katalog `media`, **gleicher** Token und Prüfpfad. Die Zustellung der Playlists ist offen (Phase 1), und es ist keine Zimmersteuerung im Sinn des Konzepts.
- **Weckruf** (`wake.set`, `wake.cancel`): gehört in den **Raum-Katalog** (SR), weil der Controller zur Weckzeit Licht, Heizung und Ton auslöst (Konzept 16.5). Das Gerät ruft ihn nur auf.
- **Gerätelokales** (Lautstärke des Geräts, Stumm, LED, Hilfetaste): bleibt im Gerät. Die Hilfetaste ohne KI meldet über den Gateway an das Team, ist keine Raum-Aktion.
Wissen, Anfragen und Rückruf laufen weiter über die Lio-Router-Werkzeuge, nicht über den Raum-Dienst.

### Q5 Scan-Ereignis B-27 und QR-Schild als ein Ereignis

| Feld | Inhalt |
|---|---|
| `event` | `room_scan` |
| `businessId`, `room` | aus dem Code auf dem Schild oder aus dem NFC-Tag |
| `stayId` | erst nach erfolgreicher Prüfung, sonst `null` (kein Rückschluss auf Personen bei Fehlversuch) |
| `ts` | Zeitpunkt |
| `source` | `qr` oder `nfc` |
| `outcome` | `opened`, `token_issued`, `check_failed` (ohne Angabe, welcher Teil falsch war) |
| nie enthalten | Name, Rufnummer, IP, Gerätekennung des Handys, Audio |

Ein Zähler, ein Schema, abgelegt über den in B-27 vorgesehenen Weg (hub-atlas, dann hub-iot; das Binding von hub-atlas fehlt noch und gehört zu B-27). Kennzahlen: Anteil der Aufenthalte mit mindestens einem `token_issued` und Anteil mit mindestens einer Anfrage an Lio. Rotprobe: Ein Scan mit Buchungsabsicht darf nie einen Rechnungsposten erzeugen, der Test muss rot werden, wenn die Sperre entfällt. Schwellen für den Erfolg legt Thomas fest.

## 7. Nachtrag 22.11. 20:34: Entscheidungen von Thomas

- **Gerät fest im Smartroom, mobil ohne Smartroom.** Zwei Betriebsarten. *Fest:* Zimmer ist bei der Installation hinterlegt (Personal, Cockpit), das Gerät ist nur im Gastzugangsfenster für Gastwünsche aktiv, kein Ausleihvorgang; Geräte-Token hängt am Zimmer plus Gerät. *Mobil:* Ausleihe nach Abschnitt 6 Q1. Beide nutzen denselben Prüfpfad und denselben Katalog. Der Smartroom-Thread ergänzt die Spezifikation (Abschnitte 3.4a und 8 gehen bisher von mobil aus).
- **IR Controller bei festem Gerät:** Sichtlinie lässt sich bei der Installation festlegen, mein Einwand A3 gilt dort schwächer. Empfehlung bleibt, ihn getrennt zu lassen, solange CE nicht geklärt ist (Thomas will keine externe Klärung). Ein Einbau wird nur auf sein Wort geplant.
- **Weg B:** eigener Hub-Agent mit demselben Funktionsumfang wie Vendasta-Lio (Capabilities, Tools, Wissensbasis). Das ist der größte Aufwandsposten und wird im Gesprächskern-Entwurf (G6) beziffert.
- **CE:** keine externe Klärung jetzt. Bleibt als bekanntes Risiko vor jedem Gästepilot im Gerät stehen.
- **Offene Hardware-Option (Hinweis Smartroom 22.11.):** Weil das Gerät in Bergsonne fest steht, ist ein Einbau des IR Controllers ins Gerät wieder denkbar (Sichtlinie planbar). Die Spezifikation bleibt bei getrennt, bis die Maße von Shelly vorliegen. Im Hardwareentwurf als **Option B** geführt, mit den Bedingungen: Maße und IR-Winkel von Shelly, Sichtlinie zum Fernseher je Zimmer geprüft, CE-Risiko von Thomas getragen.
