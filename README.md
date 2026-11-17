# rainmaker-lio-appliance

Die InRoom Appliance für Lio: ein offenes, mobiles Sprachgerät fürs Gästezimmer. Es ersetzt Alexa, Google Home und HomePod durch ein Gerät, das nur Lio kann, nur nach dem Wake-Word Audio sendet und dem Haus gehört.

Stand und Entscheidungen: [STAND.md](STAND.md). Beginne dort. Entschieden: Variante D (Eigenbau ESP32-S3), Musik nur aus den Bergsonne-Playlists des Controllers.

## Inhalt

| Pfad | Was |
|------|-----|
| `docs/entscheidungsvorlage/` | Marktanalyse und Entscheidungsvorlage vom 13.11.2026 (`.docx` im rainmaker-Layout, Quelltext `inhalt.md`, Metadaten `meta.json`) |
| `docs/vendasta/` | Anfrage an Vendasta zur Anbindung der Appliance an die AI Workforce (Entwurf, nicht gesendet) |
| `docs/recherche/` | Marktnotizen mit Quellen: loratech, Shelly, Home Assistant Voice PE, Eigenbau, offene Software |

Hardware-, Firmware- und Gateway-Code kommt erst nach der Freigabe der Phasen (siehe STAND.md).

## Regeln

- Die Rückrufnummer des Hauses und andere Telefonnummern gehören nie in dieses Repo.
- Keine Schlüssel, Tokens oder Kundendaten ins Repo. Geheimnisse nur über `rmsec` in den Hub.
- Musik nur aus den Bergsonne-Playlists vom Controller, keine Streamingdienste.
- Ein Geräteereignis erzeugt nie eine Verpflichtung (Hub-Entscheidung R-2026-09-19-12, Karte B-27).
- Dokumente für Kunden und Geschäftsführung entstehen im rainmaker-Layout und liegen zusätzlich im Brand-Ordner `rainmaker-brand`.
