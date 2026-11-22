# Phase 0: Nachfragetest B-27, Entwurf des Testplans

Status: Entwurf vom 14.11.2026, am 22.11.2026 mit dem QR-Schild von rainmaker.smartroom zusammengeführt (siehe Abschnitt „Scan-Ereignis“). Alle Werte sind Vorschläge und von Thomas zu bestätigen. Grundlage: Hub-Karte B-27 und Entscheidung R-2026-09-19-12 im Repo rainmaker-hub.

Frage des Tests: Nutzen Gäste einen NFC-Tag oder QR-Code im Zimmer, um Lio und Hotelleistungen zu erreichen? Das ist ein Stellvertreter für die Nachfrage nach einem Sprachgerät. Es kommt keine neue Hardware ins Zimmer.

Ablauf laut Karte: NFC oder QR, dann hub-atlas, dann hub-iot.

## Zu entscheiden (Vorschlag in Klammern)

| Nr. | Punkt | Vorschlag |
|-----|-------|-----------|
| 1 | Haus und Zimmer | Bergsonne, drei bis fünf Zimmer |
| 2 | Dauer | vier Wochen |
| 3 | Erfolgskriterium als Zahl, vorab festgelegt | Anteil der Aufenthalte mit mindestens einem Scan und Anteil mit mindestens einer Anfrage an Lio. Schwellen legt Thomas fest. |
| 4 | Was ein Scan auslösen darf | nur Information oder Anfrage an Lio, nie Buchung oder Abrechnung |
| 5 | Datenschutz | Scan wird nur dem Aufenthalt zugeordnet, kein Audio, keine Personendaten im Ereignis |
| 6 | Auswertung | Bericht nach der Laufzeit, Entscheidung über Phase 1 mit den Zahlen |

## Invariante und Rotprobe

Ein Geräteereignis erzeugt nie eine Verpflichtung. Die Rotprobe ist ein Test, der einen Scan mit Buchungsabsicht simuliert und prüft, dass kein Rechnungsposten entsteht. Er muss rot werden, wenn die Sperre entfernt wird.

## Abhängigkeiten

- hub-iot ist laut Konfiguration nicht öffentlich erreichbar (workers_dev aus, keine Route). Ob das Deployment dem entspricht, prüft ein eigener Thread im Hub.
- In hub-atlas steht noch kein Service Binding auf hub-iot. Es muss im selben Change wie die Funktion hinzukommen.
- Die Roadmap-Idee zur Karte liegt außerhalb der Repos und fehlt hier.

## Ergänzende Messung

Zusätzlich auswerten, wie oft Gäste Lio heute schon per Telefon und Chat nutzen. Das zeigt, wie groß die Grundnachfrage nach Lio im Zimmer ist.

## Scan-Ereignis (22.11.2026, gemeinsam mit rainmaker.smartroom)

Das QR-Schild im Zimmer (Smartroom, Konzept 11.1) und der NFC/QR-Test dieser Karte sind **dasselbe** Ereignis, es gibt nur einen Zähler. Felder, Datenschutz und Kennzahlen: `docs/smartroom/abgleich-und-schnittstelle-2026-11-22.md`, Abschnitt 6 (Q5). Kurz: `event=room_scan`, Zimmer, Aufenthalt erst nach erfolgreicher Prüfung, Zeit, Quelle `qr` oder `nfc`, Ergebnis `opened`, `token_issued` oder `check_failed`. Nie enthalten: Name, Rufnummer, IP, Handy-Kennung, Audio. Ein Scan löst nie eine Buchung aus (Rotprobe wie oben).

Folge für Punkt 4 der Tabelle „Zu entscheiden“: Ein Scan darf jetzt zusätzlich den Zimmertoken-Ablauf des Smartrooms starten (einmalige Namensprüfung). Das ist erlaubt, weil der Token nur Zimmersteuerung freigibt und nie eine Buchung. Thomas bestätigt das, ebenso Schwellen und Dauer.
