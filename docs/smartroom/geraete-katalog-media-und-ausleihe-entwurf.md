# Gerätekatalog `media`, Hilfetaste und Ausleihe: Entwurf (G14, G8, G16)

Stand: 22.11.2026, Entwurf von rainmaker.appliance. Katalog, Fehlercodes und Token-Regeln des Raum-Dienstes stehen **nur** in `smartroom-spezifikation-schnittstelle-phasen-v1-2026-11-22.md` (Eigentümer rainmaker.smartroom). Hier steht nur, was für das Gerät dazukommt. Nichts davon ist gebaut.

## 1. Katalog `media` (nur Musik, nur Bergsonne-Playlists)

Gleiche Hülle, gleiches Token (`kind: appliance`), gleicher Prüfpfad, gleiche Wiederholsperre wie der Raum-Katalog. Eigener Katalog, weil die Zustellung der Playlists offen ist und Musik keine Zimmersteuerung im Sinn des Konzepts ist.

| Aktion | Eingabe | Regel |
|---|---|---|
| `media.play` | `playlistId` aus der Liste, die der Controller bereitstellt | Nur freigegebene Playlists des Hauses, keine freie URL, kein Streamingdienst |
| `media.stop` | – | immer erlaubt |
| `media.volume` | `level` 0 bis `max` | Höchstlautstärke legt das Haus fest, Nachtruhe-Grenze (Ruhezeit des Hauses) gilt |
| `media.list` | – | Playlists, die für dieses Zimmer freigegeben sind |

Zusätzliche Fehlercodes (nur Vorschlag, in die Spezifikation übernimmt der Eigentümer): `PLAYLIST_UNKNOWN`, `QUIET_HOURS`. Alle anderen Codes aus der Spezifikation.

Offen (Phase 1, nur lesen beim Controller): Liefert der Controller die Playlists als Stream an das Gerät, oder gibt er eine Stream-Adresse heraus? Das entscheidet, ob das Gerät sich mit dem Controller verbindet (Netz, Rechte) oder nur der Hub. Nichts am Controller wird dafür geändert.

## 2. Hilfetaste und Gerätelokales

- **Hilfetaste (ohne KI):** meldet über den Gateway-Worker Zimmer und Zeit an das Team (Mail oder Aufgabe über den vorhandenen Weg des Hauses). Keine Raum-Aktion, keine Buchung. Ohne Verbindung blinkt die LED „nicht verbunden“.
- **Im Gerät, nicht im Hub-Katalog:** Gerätelautstärke, Stumm, LED, Stummschalter (stromlos). Der Hardware-Stummschalter gewinnt immer.
- **Weckruf:** `wake.set` und `wake.cancel` liegen im Raum-Katalog des Smartrooms, das Gerät ruft sie nur auf.

## 3. Cockpit-Oberfläche für Geräte (G16): Betriebsart `fixed` (Installieren) und `stay` (Ausleihe)

Entscheidung Thomas 22.11.: im Smartroom (Bergsonne) ist das Gerät **fest** (`binding: fixed`), ohne Smartroom **mobil** (`binding: stay`). Beide nutzen dieselbe Geräteseite.

**Installieren (`fixed`, Rolle Technik, nach Smartroom-Spezifikation v1.3):** Technik wählt im Cockpit das Gerät (Sockel-Tag oder QR) und das **Zimmer aus der Zimmerliste**, bestätigt den Koppelcode. Das Token hängt am Zimmer plus Gerät, hat keinen Ablauf mit dem Aufenthalt, ist aber erneuerbar und sperrbar. Gastwünsche nimmt das Gerät nur bei `InHouse`. Umsetzen in ein anderes Zimmer nur durch Technik, mit Protokoll. Die Ausgabe-Rolle gibt in dieser Betriebsart nicht aus.

**Ausleihe (`stay`, Rolle des Teams für die Ausgabe, früher „Rezeption“ genannt; das Haus hat keine Rezeption):** der folgende Ablauf, Details in `abgleich-und-schnittstelle-2026-11-22.md` Abschnitt 6 Q1. Er gilt **nur** für die Betriebsart `stay`.

## 3a. Anforderungen an die Staff-Seite

Ablauf siehe `abgleich-und-schnittstelle-2026-11-22.md`, Abschnitt 6, Q1. Anforderungen an die Staff-Seite (Bau durch Pulse, Anforderung gemeinsam mit Smartroom):

1. Eigene Staff-Anmeldung, nie über einen Gästetoken. Rollen laut Smartroom-Spezifikation 7a: Ausgabe-Rolle des Teams (früher „Rezeption“; ausgeben, zurücknehmen, sperren, Gast-Token widerrufen), Technik (Geräteübersicht, Firmware, **kein** Token-Widerruf), Hausdame (nur Wünsche und Meldungen), Leitung. Eine gemeinsame Geräteseite für Raumtechnik und Lio-Geräte, nicht zwei.
2. **Ausgeben:** Aufenthalt aus der Liste der aktuellen Aufenthalte wählen (kein freies Eingabefeld für Zimmer oder Namen), Gerät per Sockel-Tag oder QR wählen, Koppelcode vom Gerät bestätigen.
3. **Zurücknehmen:** Gerät scannen, „Zurück“, Token entzogen, Gedächtnis des Aufenthalts gelöscht.
4. **Sperren:** „Gerät verloren“ sperrt sofort, Eintrag im Protokoll.
5. **Übersicht:** je Gerät Zuordnung (Zimmer, Aufenthalt, seit wann), letzter Kontakt, Firmwarestand, Batterie nicht relevant (Netzbetrieb), Verbindungsstatus. Geräte ohne Kontakt über eine Frist erscheinen rot.
6. **Protokoll:** Wer hat wann ausgegeben, zurückgenommen oder gesperrt. Ohne Gästenamen, nur Zimmer und Aufenthalts-ID.
7. Datenschutz: Die Seite speichert keinen Gastnamen. Verzeichnis der Verarbeitungstätigkeiten wird um „Gerätezuordnung zu Aufenthalt“ ergänzt (Smartroom 16.2 deckt Zimmertoken ab, das Gerät ist ein Zusatz).
