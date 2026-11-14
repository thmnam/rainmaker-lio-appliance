# Stand

Letzte Änderung: 13.11.2026 (Variante D und Musikregel entschieden)

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

Antwort vom 13.11.2026 (Tobin Arul): Das Team meldet sich dazu, welche der Wege verfügbar sind. Eine inhaltliche Antwort steht noch aus.

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
