# Gym-Tracker – Release, Datenschutz und kostenloser Launch

Stand: 21.11.2026. Getestet im Chromium-Emulator über einen lokalen Webserver, nicht auf einem echten iPhone/Android-Gerät.

## 1. Wie die App technisch aufgebaut ist
- Eine statische Webseite: `index.html` (enthält Code, Stil und Übungsbilder), `sw.js` (Offline), `manifest.webmanifest` + Icons (Installation), `datenschutz.html`.
- **Kein Backend, keine Datenbank, kein Login, keine Passwörter, keine Sessions, keine Umgebungsvariablen, keine Geheimnisse.** Deshalb entfallen Registrierung, Login-Tests, Passwortspeicherung, Benutzertrennung auf dem Server und API-Sicherheit. Jede Person hat ihre Daten nur auf ihrem eigenen Gerät; niemand kann fremde Daten sehen.
- Ausnahme: In der Claude-Version (nicht auf GitHub Pages) gibt es optional Freunde/Cloud-Backup über die Claude-Plattform. Auf der öffentlichen Webseite ist das inaktiv.

## 2. Datenflüsse (Datenschutz)
| Daten | Wo gespeichert | Geht ins Internet? |
|---|---|---|
| Trainings, Pläne, Sätze, Rekorde | localStorage des Browsers | Nein |
| Körpergewicht, Verletzungen, Ernährung | localStorage | Nein |
| Apple-Health-Export, Kurzbefehl-Daten | nur gelesen, Ergebnis in localStorage | Nein |
| Backup-Datei | dort, wo du sie speicherst | nur wenn du sie selbst z. B. in iCloud legst |
| Aufruf der Webseite (IP-Adresse, Browser) | Server-Logs des Hosters | Ja, technisch unvermeidbar beim Laden |

Geprüft: Beim Testlauf (Start, Training, Offline, Datenschutzseite) gingen **0 Anfragen an fremde Server**. Eine Content-Security-Policy (`connect-src 'self'`) blockiert zusätzlich jede Übertragung an Dritte. Keine Cookies, kein Tracking, keine Analyse, keine externen Schriften.

Rechte der Nutzer:
- Export: Profil › Einstellungen › Backup (JSON), Rücksicherung getestet: identische Daten.
- Löschung: Profil › Einstellungen › Datenschutz & Löschen › „Alle Daten löschen“ (Bestätigung durch Eintippen von LÖSCHEN). Getestet: alle Trainings, Verletzungen, Einstellungen und Offline-Caches weg.

## 3. Rechtliche Punkte vor dem öffentlichen Launch (keine Rechtsberatung)
1. **Datenschutzerklärung:** `datenschutz.html` ist ein technisch korrekter Entwurf mit Platzhaltern [in eckigen Klammern]. Vor Veröffentlichung ausfüllen und prüfen lassen (z. B. Datenschutz-Generator oder Anwalt). Besonders: Übermittlung von IP-Adressen an den Hoster in den USA.
2. **Impressum:** Nach § 5 DDG Pflicht für „geschäftsmäßige“ digitale Dienste. Der Begriff wird weit ausgelegt; schon Werbung, Einnahmen oder die Nutzung zur Bewerbung eines eigenen Angebots (z. B. deines Kanals) können reichen. Rein private Seiten sind ausgenommen. Vorlage steht unten in `datenschutz.html`.
3. **Gesundheitsdaten (Art. 9 DSGVO):** Werden nur lokal verarbeitet und nicht an dich übertragen. Trotzdem in der Datenschutzerklärung erwähnt.
4. **Kein Medizinprodukt:** Tagesform/Erholung sind Schätzungen; Hinweis „ersetzt keine ärztliche Beratung“ steht in der Datenschutzerklärung. Keine Heilversprechen in Beschreibung oder Werbung.
5. **Bilder/Marken:** Übungsbilder und Namen (z. B. „Hevy“, „Apple Health“) – Herkunft und Nutzungsrechte der Bilder vor öffentlichem Launch klären.

## 4. PWA-Prüfung
| Punkt | Ergebnis |
|---|---|
| Manifest (Name, Farben, standalone, Icons any + maskable, id, lang) | erledigt |
| iOS: apple-touch-icon, Statusleiste, Vollbild | vorhanden |
| Android: maskierbares Icon mit Sicherheitsrand | neu: `icon-maskable-512.png` |
| Service Worker registriert | getestet: ja |
| Offline starten, Daten anlegen, nach Neuladen erhalten | getestet: ja |
| Offline-Hinweis | Meldung „Offline – alles wird auf dem Gerät gespeichert“ |
| Dauerhafter Speicher | App fragt `navigator.storage.persist()` an; Status in Einstellungen |
| iOS-Speicherlöschung | Safari löscht Daten von Webseiten nach 7 Tagen ohne Nutzung; **zum Home-Bildschirm hinzugefügte Web-Apps haben einen eigenen Zähler** und sind davon praktisch nicht betroffen. Empfehlung an Nutzer: App installieren + Backup. |

## 5. Kostenloses Hosting (Bedingungen geprüft am 21.11.2026)
| Anbieter | Kosten | Wichtige Limits | Passt? |
|---|---|---|---|
| **GitHub Pages (empfohlen, läuft schon)** | 0 € | Repo muss **öffentlich** sein (Free-Plan); Seite max. 1 GB; weiches Limit 100 GB Traffic/Monat; HTTPS automatisch | Ja. App = ca. 2,3 MB → grob 40.000 Erstaufrufe/Monat, wiederkehrende Aufrufe kommen großteils aus dem Offline-Cache |
| Cloudflare Pages | 0 € | unbegrenzt Traffic/Anfragen; 500 Deploys/Monat; max. 20.000 Dateien, 25 MiB pro Datei | Ja, beste Ausweichlösung bei viel Traffic |
| Netlify | 0 € | 300 Credits/Monat, harte Grenze; bei Überschreitung wird die Seite bis Monatsende pausiert | Möglich, aber riskanter |

Backend/Datenbank: nicht nötig, also keine Kosten. Kostenpflichtig würde es erst bei: eigener Domain (jährliche Gebühr beim Registrar), Cloud-Sync zwischen Geräten, KI-Foto-Kalorien (braucht einen Server mit API-Schlüssel), App-Store (Apple 99 $/Jahr). **Nichts davon ist eingerichtet; ich habe nichts veröffentlicht und keine kostenpflichtigen Dienste genutzt.**

Erwartete monatliche Kosten: **0 €**.

## 6. Release-Tests (durchgeführt)
| Test | Ergebnis |
|---|---|
| Registrierung / Login / Berechtigungen | nicht anwendbar (kein Konto) |
| Plan anlegen, Übung hinzufügen, Training starten, Werte eintragen, beenden, speichern | bestanden (12 Schritte) |
| Daten nach Neuladen erhalten | bestanden |
| Statistik = gespeicherte Daten (77,5 kg × 9 → 698 kg) | bestanden |
| Mobile 320/390/768/1280 px | bestanden (Crawl ohne Fehler) |
| Fehleingaben (5000 kg, 999 Wdh, negative Werte, leere/doppelte Plannamen, Code im Namen) | abgefangen |
| Verbindungsabbruch (offline Daten anlegen, wieder online) | bestanden |
| Export + Wiederherstellung, kaputte Backup-Datei | bestanden |
| Komplett löschen | bestanden |
| Keine Daten an Dritte, CSP ohne Verstöße | bestanden |
| Echtes iPhone/Android, VoiceOver, Safari-Eigenheiten | **nicht getestet** – bitte selbst prüfen (Checkliste unten) |

## 7. Veröffentlichung – Schritt für Schritt (GitHub Pages)
1. **Vorbereiten:** `datenschutz.html` öffnen und alle [Platzhalter] ersetzen (Name, Anschrift, E-Mail, Datum, Hoster-Link). Impressum-Frage klären.
2. **Build:** liegt fertig vor – kein Build-Schritt nötig. Dateien: `index.html`, `sw.js`, `manifest.webmanifest`, `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png`, `datenschutz.html`.
3. **Hochladen:** github.com › Repo `GYMs` › Add file › Upload files › alle Dateien oben auswählen › Commit. Die Commit-Historie ist automatisch dein **Rollback**.
4. **Hosting prüfen:** Settings › Pages › Source „Deploy from a branch“, Branch `main`, Ordner `/ (root)`. Repo-Sichtbarkeit: Public (Pflicht im Free-Plan). Der Quellcode ist damit öffentlich – er enthält keine Geheimnisse und keine Nutzerdaten.
5. **HTTPS:** bei `*.github.io` automatisch. „Enforce HTTPS“ anhaken, falls sichtbar.
6. **Umgebungsvariablen/Geheimnisse:** keine vorhanden, nichts zu tun.
7. **Domain (optional, kostet beim Registrar):** Settings › Pages › Custom domain eintragen, beim Registrar CNAME auf `frepologe-sudo.github.io` setzen, danach HTTPS erzwingen.
8. **Öffentliche Version testen (iPhone):** Seite in Safari öffnen › Teilen › „Zum Home-Bildschirm“ › App öffnen › Training anlegen › Flugmodus an › App schließen und öffnen → Daten müssen da sein › Datenschutz-Link in Einstellungen prüfen.
9. **Rollback:** Repo › `index.html` › History › vorherige Version öffnen › „…“ › Revert, oder alte `index.html` erneut hochladen. Nutzer bekommen die Version beim nächsten Online-Start (Service Worker lädt zuerst aus dem Netz).

## 8. Offene Fragen
- Datenschutzerklärung/Impressum: Platzhalter ausfüllen, rechtlich prüfen lassen.
- Herkunft/Lizenz der Übungsbilder klären, bevor die App beworben wird.
- Echtgeräte-Test iOS und Android.
- Cloud-Sync zwischen Geräten gibt es öffentlich nicht; Gerätewechsel nur per Backup-Datei.
