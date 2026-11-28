# Stand

Letzte Änderung: 22.11.2026 (Abgleich mit rainmaker.smartroom). Davor 13.11.2026: Variante D und Musikregel entschieden.

## Empfehlung

Eigenbau auf ESP32-S3 mit XMOS-Mikrofonfeld, 2.1-Lautsprecher und Holzgehäuse (Variante D). Die Sprache läuft über einen eigenen Agenten im Hub (EU, Cloudflare), der die Werkzeuge des Lio-Routers direkt aufruft. Details: `docs/entscheidungsvorlage/`.

## Entschieden (Thomas, 13.11.2026)

- **Variante D** (Eigenbau ESP32-S3 mit XMOS-Mikrofonfeld, 2.1-Lautsprecher, Holzgehäuse) ist freigegeben.
- **Musik:** Die Appliance spielt ausschließlich die Bergsonne-Playlists, die der Haus-Controller bereitstellt. Keine Streamingdienste, keine anderen Quellen. Offen ist nur die technische Zustellung (Stream vom Controller oder Stream-Adresse), Klärung in Phase 1.

## Offene Entscheidungen (Thomas)

| Nr. | Entscheidung | Empfehlung |
|-----|--------------|------------|
| 1 | Lio-Weg: eigener Sprachagent im Hub statt Telefonweg über Vendasta | Ja, Telefon nur als Notlösung |
| 2 | Nachfragetest B-27 zuerst, daneben nur Laborgeräte bis etwa 500 Euro | Ja |
| 3 | Gespräch mit loratech.io (Partner, mögliche Übernahme) | Ja, ergebnisoffen. Noch niemand kontaktiert. |
| 4 | Pilothaus Bergsonne, drei bis fünf Zimmer | Ja |

## Vendasta

Am 04.11.2026 ging eine kurze Mail "AI Workforce Conversation API" an Jehan Zouak, Dustin Walker und Josh Baker, ohne sichtbare Antwort. Der ausführliche Nachfass vom 13.11.2026 ist gesendet. Text: `docs/vendasta/anfrage-nachfass.md`.

## Vendasta

Antwort vom 16.11.2026 (Tobin Arul): Text-API, eigener Kanal in Konversationen und Sprach-API ohne Rufnummer gibt es nicht, ohne Termin. Geplant ist ein MCP-Zugang zu den Plattform-Werkzeugen, angepeilt später im ersten Quartal 2027, ohne Zusage.

Möglicher Umweg über dokumentierte Bausteine (API-Aufruf startet Automation, KI-Mitarbeiter beantwortet, Webhook liefert die Antwort): `docs/vendasta/workaround-automation-kette.md`. Noch nicht getestet.

## Entwürfe im Repo

- `docs/loratech/fragenkatalog.md` für das Partnergespräch
- `docs/phase-0/b27-testplan.md` mit den zu entscheidenden Punkten

## Nicht belegt

- Kosten je Gerät (150 bis 350 Euro) sind Annahmen, keine Angebote.
- loratech: TV-Steuerung, Seitenanzeige und eine Kamera im Hotel-Cube sind auf der öffentlichen Seite nicht belegt.
- Vendasta: eine öffentliche Schnittstelle für eigene Geräte an einen AI Employee wurde in der Dokumentation nicht gefunden.
- Shelly Wall Display XL: ob eigene Android-Apps erlaubt sind, ist offen.
- Vendasta Voice läuft in Deutschland (Bergsonne), aber nur als Rufnummer. Eine API für Geräte fehlt, die Anfrage ist als Entwurf bereit.

## Nächste Schritte

1. Thomas entscheidet die vier offenen Punkte oben.
2. B-27 Nachfragetest im Hub starten (Karte `docs/program/cards/B-27-...` im Repo rainmaker-hub).
3. Phase 1 Labor: Voice PE und Waveshare ESP32-S3-AUDIO an einen Prototyp-Gateway, Weg B gegen Weg A hören.
4. Additive Route im Lio-Router: Gästeaufenthalt aus Zimmer und Aufenthalt statt Rufnummer.

## Abgleich mit rainmaker.smartroom (22.11.2026)

Bestandsaufnahme (nur Papier, keine Hardware, keine Firmware, keine Termine), Abweichungen zum Konzept Zimmersteuerung und Entwurf der Schnittstelle zum Raum-Dienst: [docs/smartroom/abgleich-und-schnittstelle-2026-11-22.md](docs/smartroom/abgleich-und-schnittstelle-2026-11-22.md). Empfehlung dort: IR Controller nicht ins Gerät einbauen, CE und Funk als eigenen Punkt vor dem Pilot klären.

## Entscheidungen Thomas vom 22.11.2026 (20:34)

- **Betriebsarten:** Im Smartroom ist das Gerät **fest** im Zimmer, ohne Smartroom **mobil** (ausleihbar). Das Gerät muss beides können (Zimmerbindung fest oder per Token bei Ausleihe).
- **Lio-Weg:** Eigener Sprachagent im Hub (Weg B) mit denselben Capabilities, Tools und derselben Wissensbasis wie heute bei Vendasta. Vendasta bietet vor Q2 2027 keine API oder Anbindung. Der Telefonweg ist keine Option mehr.
- **loratech:** raus, kein Gespräch.
- **Threads:** rainmaker.appliance und rainmaker.smartroom arbeiten verbunden (gemeinsame Liste `smartroom/gemeinsame-aufgabenliste-smartroom-appliance.md`), Scan-Ereignis von QR-Schild und B-27 ist eines.
- **CE und Funk:** keine externe Klärung jetzt (Thomas: Nein). Offenes Risiko, keine Aktion.
- **IR Controller:** Anfrage (Maße, IR-Winkel, lokale Befehle) läuft, Antwort steht aus.
- Weiter offen: Pilot Bergsonne mit Zimmerzahl, B-27 zuerst, Kaufwort für Laborgeräte.

## Entscheidung Thomas vom 28.11.2026 (Punkt 19)

Rechnung einsehen und Late Check-out gehen am Zimmergerät per Sprache, aber **nicht** am öffentlichen Telefon-Lio (Datensicherheit, Datenschutz). Gerät und Telefon weichen hier gewollt ab. Festgehalten in `docs/gateway/gespraechskern-und-gateway-entwurf-v0.md` (Abschnitt 1 Ausnahme, 2b: Routen nur für Gerätetoken, Rotprobe Telefon-Aufruf abgelehnt) und in den Lio-Szenarien.
