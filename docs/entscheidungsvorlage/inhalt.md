# 0. Entscheidung in Kürze

Thomas hat Variante D am 13.11.2026 freigegeben. Wir bauen die InRoom Appliance selbst, auf einem offenen Fundament. Kern ist ein ESP32-S3 mit professionellem Mikrofonfeld, einem echten 2.1-Lautsprecher und einem Holzgehäuse. Die Sprachverarbeitung läuft in unserem eigenen EU-Backend auf Cloudflare und ruft dieselben Werkzeuge auf wie Lio heute. Ein Fertiggerät erfüllt unsere Anforderungen an Klang, Design und Lio-Anbindung nicht.

:::callout
**Empfehlung.** Eigenbau auf ESP32-S3 mit XMOS-Mikrofonfeld und Holzgehäuse (Variante D). Das Home Assistant Voice Preview Edition dient sofort als Entwicklungsgerät. Mit loratech.io führen wir ein Partnergespräch über Gehäuse, Pilotmarkt und eine mögliche Übernahme, bauen aber nicht auf dem LORA Cube auf.
:::

Drei Gründe tragen die Empfehlung. Erstens: Kein Fertiggerät, das wir gefunden haben, bietet zugleich tiefen Klang, ein hochwertiges Gehäuse, PoE oder USB-C und eine offene Schnittstelle zu Lio. Zweitens: Die Elektronik ist ein gelöstes Problem. Offene Referenzdesigns und ein dokumentierter Hoteleinsatz zeigen, dass ein ESP32-S3 mit Echounterdrückung für ein Zimmergerät genügt. Drittens: Unsere Wertschöpfung liegt nicht im Gerät, sondern im Zusammenspiel aus Lio, Hub und Haussteuerung. Genau das bleibt bei Eigenbau in unserer Hand.

## 0.1 Entscheidungen

| Nr. | Entscheidung | Empfehlung |
|-----|--------------|------------|
| 1 | Variante D (Eigenbau ESP32-S3) als Zielrichtung | Freigegeben von Thomas am 13.11.2026 |
| 2 | Lio-Weg: eigener Sprachagent im Hub statt Telefonweg über Vendasta | Ja, Telefonweg nur als Notlösung |
| 3 | Nachfragetest B-27 zuerst, daneben nur Laborgeräte bis etwa 500 Euro | Ja, Kleinserie erst nach dem Ergebnis |
| 4 | Gespräch mit loratech.io als Partner und mögliche Übernahme | Ja, ergebnisoffen |
| 5 | Pilothaus Bergsonne, drei bis fünf Zimmer | Ja |

# 1. Anforderungen und Leitplanken

Die Anforderungen stammen aus der Anfrage von Thomas vom 13. November 2026. Die rechte Spalte zeigt, was sie technisch und organisatorisch bedeuten.

| Anforderung | Konsequenz für das Design |
|-------------|---------------------------|
| Offen statt Alexa, Google Home oder HomePod | Eigener Sprachpfad, keine Fremdcloud mit Profilbildung. Wake-Word auf dem Gerät. |
| Gäste fühlen sich nicht abgehört | Hardware-Stummschalter, der die Mikrofone stromlos macht. Sichtbare LED. Kein Audio vor dem Wake-Word. Keine Kamera. |
| Keine unnötigen Funktionen | Nur Lio: fragen, empfehlen, anfragen, buchen, Zimmer steuern. Kein Musikdienst-Zoo, kein Shopping. Musik nur aus den Bergsonne-Playlists des Haus-Controllers (Entscheidung Thomas, 13.11.2026). |
| Strom per PoE oder USB-C | PoE+ (802.3at) oder USB-C PD mit 12 Volt, denn ein Bassverstärker braucht Spitzenleistung. |
| Netz per PoE oder WLAN | Ein Gerät mit Ethernet über PoE und WLAN als Alternative. |
| Mobil, nicht fest installiert, Gast nimmt es optional mit aufs Zimmer | Ausleihe am Empfang. Kopplung an Zimmer und Aufenthalt per NFC oder QR. Beim Zurückgeben setzt sich das Gerät zurück. |
| Direkt an Lio angebunden | Siehe Kapitel 3. Der Weg ist die wichtigste offene Architekturfrage. |
| Gute Mikrofone, tiefer Sound | Mikrofonfeld mit Echounterdrückung und Strahlformung. Lautsprecher mit Tieftöner oder Passivradiator. Kleine 2 Watt Lautsprecher scheiden aus. |
| Holz oder edles Material, kein Kunststofflook | Eigenes Gehäuse. Das ist der größte Kostenhebel in der Kleinserie. |

## 1.1 Leitplanken aus unserem Bestand

Aus dem Programm gilt die Entscheidung R-2026-09-19-12: Phase 0 kommt vor jeder Hardware, und ein Geräteereignis erzeugt nie eine Verpflichtung, also keinen Rechnungsposten. Das ist Karte B-27 „In-Room-IoT Phase 0: Nachfragetest“. Die Appliance darf deshalb nie selbst abrechnen. Buchungen laufen wie heute über die Lio-Werkzeuge und die Bestätigung durch das Haus.

Der Hub kennt bereits die Worker hub-iot, hub-smartcontrol, hub-concierge, hub-lio und hub-llm-router. Die Appliance wird ein weiterer Client dieser Bausteine, kein neues Parallelsystem. Transportregel nach Charter 3.11 bleibt: Gerät zu Hub über einen Gateway-Worker, Worker untereinander über Service Bindings.

# 2. Marktüberblick

Recherchiert wurden Hotelprodukte, offene Sprachsatelliten, Wanddisplays, Eigenbauplattformen und die Open-Source-Software dazu. Die Angaben stammen von den Herstellerseiten und Fachberichten, Quellen stehen in Kapitel 8. Was wir nicht belegen konnten, ist als offen markiert.

## 2.1 loratech.io, LORA Cube

LORAtech e.U. sitzt in Linz und bietet den LORA Cube als „holiday companion“ für Hotelgäste an. Die Webseite nennt Platzierung im Zimmer, an der Rezeption und in Gemeinschaftsräumen, mehr als 50 Sprachen, keine App und keine Registrierung, Anfragen und Buchungen von Hotelleistungen wie Spa oder späterem Check-out, und DSGVO-konforme Verarbeitung. Das Produkt ist in einer Pilotpartner-Phase. Die Firma ist ein Einzelunternehmen.

Die frei zugängliche Anleitung der Verbraucherversion beschreibt ein lokal arbeitendes Gerät mit „Hey LORA“, Spotify, Home-Assistant-Anbindung, einer lokalen Weboberfläche und Abos für ein oder zwei Geräte. Die Einrichtung läuft über einen QR-Code, den man vor die Kamera des Geräts hält. Der Hinweis, Zugangsdaten auf Papier zu notieren, weil sie bei Updates gelöscht werden, deutet auf ein frühes Reifestadium hin.

Was wir nicht belegen konnten: Die Steuerung des Fernsehers und das Anzeigen von Seiten zur Ansage, die Thomas erwähnt hat, stehen nicht auf der öffentlichen Seite. Ob der Hotel-Cube eine Kamera hat, ist offen. In einem Gästezimmer wäre eine Kamera ein Ausschlusskriterium, es sei denn, sie ist mechanisch abgedeckt oder entfällt.

Einschätzung: Interessant als Partner, Designer und Pilotmarkt. Als Fundament ungeeignet, solange die Plattform geschlossen ist und die Firma aus einer Person besteht. Eine Übernahme lohnt nur, wenn wir Quellcode, Firmware, Designrechte und die Hardwarelieferkette bekommen.

## 2.2 Shelly Wall Display XL

Das Display hat 10,1 Zoll, Android 11, ein Vier-Mikrofon-Linienfeld und zwei Lautsprecher mit je 2 Watt. Es ist für die Wandbox gebaut, mit 100 bis 240 Volt Netzanschluss, und lässt sich laut Datenblatt auch über USB-C mit 5 Volt und 3 Ampere versorgen. Alexa wird unterstützt. Es steuert Lichter und Verbraucher und passt in unser Smart-Home-Umfeld, in dem Bergsonne bereits Shelly-Geräte einsetzt.

Als Appliance scheidet es aus. Zwei 2-Watt-Lautsprecher liefern keinen tiefen Klang, das Gehäuse aus Glas und Metall ist nicht der gewünschte Holzcharakter, es gibt kein PoE, und der Wandeinbau widerspricht dem mobilen Gedanken. Offen ist, ob man eigene Android-Apps installieren darf. Als zusätzliches Zimmerpanel mit Lio-Weboberfläche taugt es später möglicherweise.

## 2.3 Home Assistant Voice Preview Edition

Ein offenes Fertiggerät von Nabu Casa. Es hat einen ESP32-S3, einen XMOS-XU316-Audiochip, zwei Mikrofone, einen eingebauten Lautsprecher, einen 3,5-Millimeter-Ausgang für externe Lautsprecher und einen Schalter, der die Mikrofone physisch stromlos macht. Strom kommt über USB-C mit 5 Volt und 2 Ampere. Software und Firmware sind quelloffen. Der Einführungspreis lag bei 59 US-Dollar.

Für uns ist es ein ideales Entwicklungsgerät und liefert die Vorlage für den Stummschalter. Als Produkt ist es zu klein im Klang, aus Kunststoff, ohne PoE und auf Home Assistant zugeschnitten.

## 2.4 Offene Software

Der offene Sprachstack ist reif. Die Bausteine sind Wake-Word-Erkennung auf dem Gerät (openWakeWord, microWakeWord), Sprache zu Text (Whisper und Nachfolger), Text zu Sprache (Piper), das Wyoming-Protokoll für Satelliten und der Linux Voice Assistant der Open Home Foundation für Raspberry-Pi-Satelliten. Für einen eigenen Agenten kommen Echtzeitframeworks wie Pipecat oder LiveKit Agents in Frage. Wir prüfen im Prototyp, welches davon zu Cloudflare Workers passt. Dabei übernehmen wir nichts ungeprüft: Lizenzen, EU-Verarbeitung und Wartungsstand klären wir je Baustein.

## 2.5 Eigenbauplattformen

Für den Kern gibt es zwei brauchbare Wege.

| Plattform | Bausteine | Passt wenn |
|-----------|-----------|------------|
| ESP32-S3 Pod | Waveshare ESP32-S3-AUDIO-Board (ES8311 Codec, ES7210 Vierkanal-ADC, Echo-Referenzkanal, USB-C, sieben RGB-LEDs, WLAN) oder ReSpeaker XVF3800 mit XIAO ESP32-S3, dazu Class-D-Verstärker und Lautsprecher | Wir nur Stimme brauchen. Kein Betriebssystem zu pflegen, Updates per OTA, Start in Sekunden, niedriger Verbrauch. |
| Einplatinenrechner | Raspberry Pi 5 oder CM5, ReSpeaker XVF3800 USB, PoE-HAT, Verstärker | Wir ein Display oder lokale Spracherkennung wollen. Dafür Betriebssystem, SD-Karten und Patches in jedem Gerät. |

Ein veröffentlichter Praxisbericht eines Hotelprojekts (iCe Gaming, veröffentlicht am 24. Oktober) bestätigt die Wahl. Ein M5Stack ATOM Echo scheiterte, weil Mikrofon und Lautsprecher sich einen Takt teilen und das Gerät nicht gleichzeitig hören und sprechen kann. Das Waveshare-Board mit eigenem Echo-Referenzkanal löste das und entfernte 11 bis 14 dB Echo, sodass Unterbrechen per Stimme funktioniert. Das Projekt koppelt außerdem das Gedächtnis an den Aufenthalt statt an das Zimmer, setzt einen Stummschalter und einen Hilfeknopf ein und signiert Firmware-Updates. Das sind Muster, die wir übernehmen, nicht der Code.

## 2.6 Geschlossene Systeme

Amazon Echo, Google Nest und Apple HomePod sowie die darauf aufbauenden Hotelprogramme scheiden aus den von Thomas genannten Gründen aus. Wir verfolgen sie nicht weiter.

# 3. Anbindung an Lio

## 3.1 Wie Lio heute erreichbar ist

Lio läuft als KI-Mitarbeiter in der Vendasta Business App. Dort sind die Kanäle Web-Chat, WhatsApp, Instagram, Facebook, E-Mail, SMS und Telefon vorgesehen. Die Telefonfunktion gehört zum Premium-Paket. Thomas bestätigt: Vendasta Voice läuft in Deutschland produktiv bei Bergsonne Allgäu. Es ist aber ein reiner Telefonkanal. Jemand muss eine Rufnummer wählen, und das passt nicht zu einem Gerät im Zimmer, das Zimmer und Aufenthalt kennt statt einer Rufnummer. Wir brauchen eine Schnittstelle. Die Sprachanbieter sind OpenAI GPT Realtime, Deepgram und ElevenLabs.

Wichtig für den Weg: In der Vendasta-Dokumentation, soweit wir sie durchsucht haben, steht keine öffentliche Schnittstelle, mit der ein eigenes Gerät Text oder Audio direkt an einen KI-Mitarbeiter schickt. Die Richtung ist umgekehrt. Lio ruft unsere Werkzeuge im Lio-Router auf, einen Cloudflare Worker mit festen Routen für Buchung, Alarm, Rückruf, Gästeaufenthalt, Link und Nachricht per SMS, stillen Anruf und Wissenspflege.

## 3.2 Drei Wege

| Weg | Prinzip | Vorteile | Nachteile |
|-----|---------|----------|-----------|
| A Telefonweg | Gerät wählt die Lio-Rufnummer des Hauses als VoIP-Endgerät, je Gerät eine eigene Kennung. Von Thomas als ungeeignet eingestuft, nur technische Notlösung | Lio und alle Werkzeuge bleiben unverändert. Schnell testbar. | Telefonie-Audio klingt schmal. Kosten je Minute. Verzögerung. Kein Unterbrechen per Stimme. Abhängig von Vendasta-Verfügbarkeit. |
| B Eigener Sprachagent im Hub | Gerät streamt Audio an einen Gateway-Worker. Dort laufen Sprache zu Text, Modell über hub-llm-router, Text zu Sprache. Der Agent ruft die Lio-Router-Werkzeuge direkt auf. | Volle Kontrolle, EU-Verarbeitung, Unterbrechen per Stimme, Kosten je Anfrage steuerbar, Passung zu Edition 5. | Wissen und Persönlichkeit von Lio müssen im Hub nachgebildet werden. Mehr Aufwand. |
| C Mischform | Pilot über A, parallel B aufbauen, dann umschalten | Früher Hörtest. | Doppelter Aufwand, wenn A nur zum Testen dient. |

## 3.3 Empfehlung

Weg B. Er ist der einzige, der Unterbrechen per Stimme, saubere Breitband-Audioqualität und eine EU-Verarbeitung nach unseren Regeln zusammenbringt. Die Werkzeuge existieren schon als einfache HTTPS-Routen, ein eigener Agent kann sie unverändert nutzen. Das Wissen liegt bereits als Faktenregister, Hausfakten und FAQ vor und lässt sich über die Wissensrouten des Lio-Routers auslesen.

Eine Lücke ist konkret benannt: Die Route für den Gästeaufenthalt erkennt den Gast heute über die Rufnummer. Eine Appliance kennt Zimmer und Aufenthalt, nicht die Nummer. Wir ergänzen eine additive Route, die Zimmer und Aufenthalt aus dem Gerätetoken auflöst. Weg A bleibt nur technische Notlösung für einen frühen Hörtest, weil er eine gewählte Rufnummer verlangt. Parallel fragen wir Vendasta nach einer Schnittstelle (Text-API, Custom Channel oder Voice-API). Der Nachfass liegt als Entwurf bereit und ist nicht gesendet. Liefert Vendasta eine Schnittstelle, prüfen wir Weg B mit Vendasta als Lio-Hirn neu gegen den eigenen Agenten.

Ein Display brauchen wir in Version 1 nicht. Wo Thomas „zeigt Seiten zur Ansage“ meint, sendet Lio einen Link per SMS (die Route dafür gibt es), zeigt Inhalte auf dem Hotelfernseher über die Haussteuerung oder auf dem Gästehandy. Das hält das Gerät schlicht.

# 4. Bewertung der Varianten

Bewertet wird mit gut, mittel und schwach. Die Einschätzung zu Kosten und Aufwand beruht auf Annahmen, solange wir keine Angebote haben.

| Kriterium | A loratech Cube | B Shelly XL | C HA Voice PE | D ESP32 Eigenbau | E Pi Eigenbau |
|-----------|-----------------|-------------|---------------|------------------|---------------|
| Kosten je Gerät | mittel, unbekannt | mittel | gut | gut in Serie | mittel |
| Aufwand bis Pilot | gut, falls offen | mittel | gut | mittel | schwach |
| Datenschutz | mittel, Kamera offen | mittel, Android und Cloud | gut | gut | gut |
| Audioqualität | unbekannt | schwach | mittel | gut | gut |
| Design und Material | mittel | mittel | schwach | gut | gut |
| PoE oder USB-C | unbekannt | nur USB-C | nur USB-C | beides | beides |
| Lio-Anbindung | schwach | mittel | mittel | gut | gut |
| Skalierung auf viele Zimmer | mittel, Lieferrisiko | mittel | mittel | gut | mittel, OS-Pflege |
| Gesamt | Partner prüfen | nur Zusatzpanel | Laborgerät | Empfehlung | Option mit Display |

# 5. Empfehlung und Zielbild

## 5.1 Architektur

Das Gerät erkennt das Wake-Word selbst. Erst danach streamt es Audio verschlüsselt an einen Gateway-Worker in unserem Hub. Der Gateway bindet das Gerät über ein Token an Zimmer und Aufenthalt, trennt Sprechende und Echo, steuert Sprache zu Text, Modellaufruf über hub-llm-router und Text zu Sprache und ruft die Lio-Werkzeuge auf. Haussteuerung (Licht, Musik, Fernseher, Sauna) läuft wie bisher über hub-smartcontrol und hub-iot zu den Haus-Controllern, auf denen in Bergsonne schon ein Raspberry Pi 5 läuft.

Musik kommt ausschließlich aus den Bergsonne-Playlists, die der Haus-Controller bereitstellt. Die Appliance bindet keine Streamingdienste an. Offen ist die technische Zustellung: ob der Controller die Playlists als Stream an das Gerät liefert oder das Gerät eine Stream-Adresse abruft. Das klären wir in Phase 1 mit dem Controller (Sonos-Controller auf dem Raspberry Pi 5 in Bergsonne). Der Hinweis auf Spotify im LORA Cube zeigt, was wir bewusst nicht bauen.

Gedächtnis gehört dem Aufenthalt, nicht dem Zimmer. Beim Zurückgeben oder Auschecken wird es gelöscht, und das Gerät setzt sich zurück.

## 5.2 Zielspezifikation Hardware

| Baustein | Vorgabe |
|----------|---------|
| Rechner | ESP32-S3 mit 16 MB Flash, OTA mit signierten Updates |
| Mikrofone | Vier-Mikrofon-Feld mit XMOS XVF3800 (Echounterdrückung, Strahlformung) oder ES7210 mit Echo-Referenz |
| Lautsprecher | 2.1: zwei Breitbänder und ein Tieftöner oder Passivradiator, Class-D-Verstärker, Richtwert 15 bis 20 Watt Spitze |
| Strom | PoE+ (802.3at) mit Splitter oder USB-C PD mit 12 Volt, wahlweise beide |
| Netz | Ethernet über PoE, WLAN als Alternative |
| Bedienung | Hardware-Stummschalter für die Mikrofone, Lautstärke, LED-Ring, Hilfetaste |
| Kopplung | NFC-Tag im Sockel für Ausleihe und Rückgabe |
| Gehäuse | Massivholz oder Furnier, Stoffbespannung, keine Kamera |

## 5.3 Datenschutz von Grund auf

Wir versprechen nur, was wir halten. Ein Cloud-Modell im Sprachpfad heißt: Audio verlässt das Zimmer, sobald das Wake-Word erkannt ist. Das Gerät schickt kein Audio davor, speichert nichts dauerhaft und wird auf Wunsch mit dem Schalter stromlos stumm. Die Verarbeitung läuft in der EU, mit Auftragsverarbeitungsverträgen für jeden Anbieter im Pfad und mit der Kostensperre und den Sperren von hub-llm-router. Ein Hinweisblatt am Gerät informiert den Gast nach Art. 13 DSGVO. „Komplett lokal“ sagen wir nicht, solange es nicht stimmt. Eine spätere Variante mit lokaler Spracherkennung auf dem Haus-Pi ist möglich.

## 5.4 Kostenrahmen

Die Zahlen sind Richtwerte, keine Angebote. Wir prüfen sie vor jeder Bestellung.

| Posten | Richtwert |
|--------|-----------|
| Elektronik (Board, Mikrofonfeld, Netzteilmodul) | 60 bis 120 Euro |
| Lautsprecher und Verstärker | 40 bis 80 Euro |
| Holzgehäuse in Kleinserie | 40 bis 150 Euro |
| Summe je Gerät | etwa 150 bis 350 Euro |
| Laborgeräte für Phase 1 (Voice PE, Waveshare, XVF3800) | unter 500 Euro gesamt |
| Betrieb je Gerät | Sprach- und Modellkosten je Anfrage, im Pilot messen |

# 6. Fahrplan

| Phase | Inhalt | Ergebnis |
|-------|--------|----------|
| 0 | Nachfragetest B-27 mit NFC oder QR. Parallel Gespräch mit loratech.io. | Belegte Nachfrage, Klarheit zu loratech |
| 1 | Labor: Voice PE und Waveshare an einen Prototyp-Gateway, Audio, Wake-Word und Echo prüfen, Weg B gegen Weg A testen | Hörtest mit Bergsonne-Wissen, Entscheidung zum Kern |
| 2 | Pilot Bergsonne mit drei bis fünf Zimmern, Ausleihe am Empfang, Gästefeedback | Messwerte zu Nutzung, Latenz, Kosten |
| 3 | Gehäuse und Kleinserie, Datenschutzprüfung, Rollout-Paket für weitere Häuser | Serienreifes Gerät |

Jede Phase endet mit einem prüfbaren Ergebnis und einer Freigabe von Thomas. Kleinserie und Gehäusebau starten erst nach Phase 2.

# 7. Risiken und offene Punkte

| Risiko oder offene Frage | Maßnahme |
|--------------------------|----------|
| Entscheidung R-2026-09-19-12 verlangt Phase 0 vor jeder Hardware | Nur Laborgeräte bis etwa 500 Euro, Kleinserie nach Nachfrageergebnis |
| Keine dokumentierte Schnittstelle von Vendasta für eigene Geräte. Voice dort nur als Rufnummer. | Weg B. Anfrage an Vendasta als Entwurf bereit, nicht gesendet. Frage zusätzlich ins Feedback-Board aufnehmen. |
| Lio-Wissen und Persönlichkeit im Hub nachbilden | Wissen über die Wissensrouten übernehmen, Testset der Concierge-Analyse verwenden |
| Der Gästeaufenthalt wird heute über die Rufnummer erkannt | Additive Route für Zimmer und Aufenthalt aus dem Gerätetoken |
| loratech: Kamera, TV-Steuerung, Anzeige, Quellcode, Rechte, Lieferfähigkeit ungeklärt | Fragenkatalog im Gespräch, Übernahme nur mit Quellcode und Designrechten |
| Echo und Rückkopplung zwischen Lautsprecher und Mikrofon | Board mit gleichzeitigem Hören und Sprechen mit Echo-Referenz, Messung im Labor |
| Cloud im Sprachpfad widerspricht dem Versprechen „nicht abgehört“ | Ehrliche Kommunikation, Wake-Word lokal, Stummschalter, EU-Verarbeitung |
| Preise, Lieferzeiten und Verfügbarkeit der Bauteile | Angebote einholen, Ersatzbauteile benennen |
| Betrieb vieler Geräte | Signierte OTA-Updates, Geräteregister, Hilfetaste ohne KI |

# 8. Quellen und Grundlagen

Interne Grundlagen: Hub-Programmkarte B-27 (In-Room-IoT Phase 0), Worker hub-iot, hub-smartcontrol, hub-concierge, hub-lio und hub-llm-router im Repository rainmaker-hub. Lio-Router README im Repository rainmaker-lio-router. Lio-Architekturregeln im Wissensrepository projekt-rainmaker-pulse. Sitzungsstände zu Smart Home: Shelly und Sonos Automation (Bergsonne, Raspberry Pi 5).

Extern, abgerufen am 13. November 2026:

LORAtech, https://loratech.io/en/ und https://loratech.io/lora_ai_assistant_tutorial/v2

Shelly Wall Display XL, https://www.shelly.com/de/blogs/documentation/shelly-wall-display-xl

Home Assistant Voice Preview Edition, https://www.home-assistant.io/voice-pe/ und https://www.cnx-software.com/2024/12/20/59-voice-preview-edition-adds-an-offline-smart-speaker-to-your-home-assistant-server

Waveshare ESP32-S3-AUDIO-Board, https://www.cnx-software.com/2025/08/30/esp32-s3-audio-board-smart-speaker-devkit-dual-mic-array-lcd-camera-rgb-leds/

ReSpeaker XMOS XVF3800, https://thepihut.com/products/respeaker-xmos-xvf3800-ai-powered-4-mic-array-for-clear-voice-even-in-noise

Praxisbericht Hoteleinsatz lokaler Sprachassistent, https://dev.to/icegaming/from-a-desk-toy-to-a-hotel-room-what-it-took-to-ship-a-local-voice-assistant-to-real-buildings-109m

Vendasta Dokumentation zu AI Voice Receptionist und Conversations, https://docs.vendasta.com/ai/ai-workforce/ai-voice-receptionist und https://docs.vendasta.com/business-app/conversations/
