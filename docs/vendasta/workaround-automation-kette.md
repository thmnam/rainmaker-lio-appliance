# Workaround ohne Vendasta-Schnittstelle: Automation als Endpunkt

Stand 17.11.2026. Alles aus der öffentlichen Vendasta-Dokumentation gelesen, **noch nichts ausprobiert**. Auslöser: Antwort von Tobin Arul vom 16.11.2026. Pfade A (Text-API), B (eigener Kanal) und C (Sprach-API ohne Rufnummer) gibt es nicht und sind nicht geplant. Ein MCP-Zugang zu den Plattform-Werkzeugen wird für später im ersten Quartal 2027 angepeilt, ausdrücklich ohne Zusage.

## Die Kette (alle Bausteine sind dokumentiert)

1. Unser Gateway ruft `POST https://prod.apigateway.co/platform/automationRuns` auf. Auth: Vendasta-Token mit Scope `business`. `attributes.entityId` ist die AG-ID des Hauses, `attributes.data` trägt unsere Nutzdaten (Frage, Zimmer, Aufenthalt, Anfrage-ID).
2. Die Automation beginnt mit dem Auslöser **"Triggered via API for an account"**. Die erwarteten Schlüssel müssen am Auslöser unter "Payload data" vorab definiert sein, sonst kommen die Daten nicht in den Schritten an.
3. Schritt **"Send a request to an AI employee"**: wählbar sind vorgefertigte und eigene Mitarbeiter (Chat Receptionist, eigener KI-Mitarbeiter), **nicht** der Voice Receptionist. Der Prompt darf höchstens 4.000 Zeichen haben, Antwort und Gedankengang kommen als dynamische Inhalte zurück. Wissensbasis, Fähigkeiten und Anweisungen des gewählten Mitarbeiters gelten.
4. Schritt **"Send a webhook"** schickt die Antwort an unser Gateway (flaches JSON, alle Werte als Text, Signatur im Header prüfen). Anfrage-ID zurückgeben, damit wir Frage und Antwort zuordnen.
5. Gateway: Antwort in Sprache wandeln und im Zimmer abspielen.

Quellen: docs.vendasta.com (Seiten "Send a request to an AI employee", "Trigger: API call on an account", "Send data out with a webhook"), developers.vendasta.com (Guides "Trigger a prebuilt automation" und "Use the send a webhook action").

## Weitere geprüfte Wege

- **Web-Chat-Widget fernsteuern:** Das Widget nimmt per URL-Hash `#webchat-send-message?webchat-widgetId=...` eine Nachricht an. Eine dokumentierte Rückgabe der Antwort gibt es nicht. Nur mit Browser-Automatisierung und Auslesen der Seite machbar. Nicht empfohlen.
- **Auslöser "Konversationsnachricht gesendet oder empfangen" plus Webhook:** Liefert Inhalt, Richtung, Kanal und Absender, aber keine dokumentierte Kontakt-ID. Zuordnung schwach. Nur als Ergänzung zur Protokollierung sinnvoll.
- **SMS oder Telefon über das Gateway:** Dokumentierte Kanäle, aber mit Kosten je Nachricht oder Minute, Rufnummer je Zimmer und der Begrüßung bei jedem Anruf. Nur als Notlösung.

## Grenzen, die wir kennen

- Die Kette ist **asynchron**: der API-Aufruf liefert eine leere Antwort, die Antwort kommt per Webhook. Die Latenz kennen wir nicht. Für ein Sprachgerät sind mehr als etwa drei Sekunden bis zur Antwort spürbar.
- Jede Anfrage ist ein **einzelner Prompt** ohne Gesprächsgedächtnis auf Vendasta-Seite. Verlauf (letzte Wechsel dieses Aufenthalts) muss das Gateway in den Prompt packen, innerhalb der 4.000 Zeichen.
- Ob die Antwort auf dem Kontakt in Konversationen und im CRM protokolliert wird, ist **nicht belegt**. Fallback: Schritte in der Automation, die eine Notiz oder Aktivität schreiben.
- Ob Custom Capabilities (unsere Lio-Router-Werkzeuge) in diesem Schritt aufgerufen werden, sagt die Seite nur allgemein ("Fähigkeiten"). Muss getestet werden. Die Fähigkeiten müssen am Chat Receptionist hängen, nicht nur am Voice Receptionist.
- Kosten je Automationslauf und Ratenbegrenzung sind **nicht dokumentiert**.
- Automationen lassen sich nur in der Oberfläche bauen, nicht per API. Der Aufbau liegt bei uns im Partner Center.
- Das ist ein Umweg über Dokumentiertes, kein zugesagter Kanal. Vendasta kann ihn ändern.

## Messplan (ein Nachmittag, noch ohne Hardware)

1. Automation "Appliance Anfrage" im Partner Center bauen (Auslöser, KI-Schritt, Webhook).
2. Webhook zunächst auf einen Wegwerf-Empfänger richten, Nutzlast ansehen.
3. 20 Testanfragen per API, jeweils Zeit bis zum Webhook messen (Median, langsamster Wert).
4. Prüfen: Antwortqualität gegenüber Lio im Chat, Werkzeugaufrufe (Hausfakten, Rückruf), Eintrag im Verlauf des Kontakts.
5. Entscheidung: Latenz und Funktionsumfang tragen ein Sprachgerät, oder wir bleiben beim eigenen Hub-Agenten.
