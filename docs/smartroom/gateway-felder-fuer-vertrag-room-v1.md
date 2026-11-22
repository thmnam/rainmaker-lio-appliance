# Felder, die der Gateway-Worker im Vertrag `room-v1` zusätzlich braucht (22.11.2026, Entwurf rainmaker.appliance)

Für SRM-0007. Namen sind Vorschläge, der Vertrag gehört dem Smartroom-Thread. Der Gateway entwirft gegen den Vertrag, nicht gegen Code.

## 1. Anfrage (Gateway an Raum-Dienst, interner Eingang)
| Feld | Zweck |
|---|---|
| `requestId` | vom Gateway je Äußerung erzeugt, Idempotenz je Äußerung |
| `source: "device"`, `deviceId` | Quelle und Gerät, muss mit `deviceId` im Token übereinstimmen |
| `conversationId` | kurzlebige Gesprächs-ID nur zur Zuordnung im Protokoll, kein Inhalt, nie im Modellkontext als Token-Ersatz |
| Token (Header) | Geräte-Token, der Gateway hält es, das Modell sieht es nie |

Die Geräte-Signatur (Gerät an Gateway: `X-Device-Kid`, `X-Device-Ts`, `X-Device-Nonce`, `X-Device-Sig` über Methode, Aktion, Body-Hash, Zeit) prüft der Gateway selbst. Der Raum-Dienst sieht davon nur `deviceId` und das Ergebnis über die interne Authentifizierung.

## 2. Antwort
| Feld | Zweck |
|---|---|
| `ok`, `state`, `assumed`, `requestId` | wie im Katalog |
| `reason` (maschinenlesbar) | der feste Fehlercode, nie Freitext |
| `limits` (`min`, `max`) bei `OUT_OF_RANGE` | Gateway sagt „16 bis 24 Grad“ ohne eigene Zahl |
| `retryAfterSec` bei `RATE_LIMITED` | Gateway sagt „gleich wieder“ |
| `state.updatedAt`, bei `assumed` zusätzlich `assumedSince` | Gateway unterscheidet „gerade gesetzt“ von „vermutlich noch so“ |

## 3. Zusätzliche Fehlercodes
`TOKEN_REVOKED` (Gerät muss sich zurücksetzen, nicht erneuern), `STAY_ENDED` (Aufenthalt vorbei, Gerät wird nach Hinweis stumm), `DEVICE_UNBOUND` (Gerät ohne aktives Token). Der Gateway behandelt `TOKEN_REVOKED` und `DEVICE_UNBOUND` anders als `TOKEN_EXPIRED` (Erneuerung versuchen).

## 4. Token (Geräte-Token)
`kind`, `biz`, `room`, `stay`, `deviceId`, `deviceKid` (Schlüsselkennung), `iat`, `exp`, `refreshAfter`, `jti`, `generation` (Widerrufszähler je Gerät).

## 5. Kopplung und Erneuerung (nicht Teil des Katalogs, gleicher Prüfpfad)
- `pair.start` (Gerät über Gateway): `deviceId`, öffentlicher Schlüssel → Koppelcode (sechs Stellen, fünf Minuten, einmalig).
- `pair.confirm` (Staff, Cockpit): Code, Aufenthalts-Verweis → Token wird dem Gerät übergeben. `pair.status` zum Abfragen durch das Gerät.
- `token.refresh` (Gerät über Gateway, signiert): neues Token nur im Gastzugangsfenster.
- `device.reset` (Gerät bestätigt Zurücksetzen nach Rückgabe oder Widerruf).

## 6. Gerätestatus für das Cockpit (Heartbeat, nicht Katalog)
`device.heartbeat`: `deviceId`, `fw` (Firmwarestand), `rssi`, `uptime`, `bound` (ja/nein), Zeit. Kein Audio, kein Gesprächsinhalt. Zeigt die gemeinsame Geräteseite.

## 7. `room_scan`
Felder wie Abschnitt 6 (Q5) des Abgleichsdokuments. Zusätzlich Fälschungsschutz: nur `token_issued` zählt in die Kennzahl, `opened` je Zimmer und Stunde begrenzt.

## 8. Eigener Vertrag `media-v1` (Katalog media)
Gleiche Hülle, gleiche Token. Aktionen und Codes: `geraete-katalog-media-und-ausleihe-entwurf.md`. Kommt erst mit Phase 5, nicht in SRM-0007.
