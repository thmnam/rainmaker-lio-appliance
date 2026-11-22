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

- **Hilfetaste (ohne KI):** meldet über den Gateway-Worker Zimmer und Zeit an die Rezeption (Mail oder Aufgabe über den vorhandenen Weg des Hauses). Keine Raum-Aktion, keine Buchung. Ohne Verbindung blinkt die LED „nicht verbunden“.
- **Im Gerät, nicht im Hub-Katalog:** Gerätelautstärke, Stumm, LED, Stummschalter (stromlos). Der Hardware-Stummschalter gewinnt immer.
- **Weckruf:** `wake.set` und `wake.cancel` liegen im Raum-Katalog des Smartrooms, das Gerät ruft sie nur auf.

## 3. Ausleih-Ablauf als Anforderung an die Cockpit-Oberfläche (G16)

Ablauf siehe `abgleich-und-schnittstelle-2026-11-22.md`, Abschnitt 6, Q1. Anforderungen an die Staff-Seite (Bau durch Pulse, Anforderung gemeinsam mit Smartroom):

1. Eigene Staff-Anmeldung, nie über einen Gästetoken. Rollen laut Smartroom-Spezifikation 7a: Rezeption (ausgeben, zurücknehmen, sperren, Gast-Token widerrufen), Technik (Geräteübersicht, Firmware, **kein** Token-Widerruf), Hausdame (nur Wünsche und Meldungen), Leitung. Eine gemeinsame Geräteseite für Raumtechnik und Lio-Geräte, nicht zwei.
2. **Ausgeben:** Aufenthalt aus der Liste der aktuellen Aufenthalte wählen (kein freies Eingabefeld für Zimmer oder Namen), Gerät per Sockel-Tag oder QR wählen, Koppelcode vom Gerät bestätigen.
3. **Zurücknehmen:** Gerät scannen, „Zurück“, Token entzogen, Gedächtnis des Aufenthalts gelöscht.
4. **Sperren:** „Gerät verloren“ sperrt sofort, Eintrag im Protokoll.
5. **Übersicht:** je Gerät Zuordnung (Zimmer, Aufenthalt, seit wann), letzter Kontakt, Firmwarestand, Batterie nicht relevant (Netzbetrieb), Verbindungsstatus. Geräte ohne Kontakt über eine Frist erscheinen rot.
6. **Protokoll:** Wer hat wann ausgegeben, zurückgenommen oder gesperrt. Ohne Gästenamen, nur Zimmer und Aufenthalts-ID.
7. Datenschutz: Die Seite speichert keinen Gastnamen. Verzeichnis der Verarbeitungstätigkeiten wird um „Gerätezuordnung zu Aufenthalt“ ergänzt (Smartroom 16.2 deckt Zimmertoken ab, das Gerät ist ein Zusatz).
