# PROJECT_PROGRESS – Gym-Tracker Premium-Überarbeitung

Stand: 2026-11-20 00:30 (Europe/Berlin)
Aktuelle Phase: **Ultimate-Auftrag inkl. offener Punkte ABGESCHLOSSEN (21.11.2026)**. Einzig BLOCKIERT: Login/Sync (braucht Firebase-Konto des Nutzers, siehe LOGIN_PLAN.md).

## Projektstruktur (Fakten)
- Arbeitsordner: `/home/claude/p9`. Build: `python3 build.py` → `p9/index.html`.
- PWA-Ausgabe: `python3 /home/claude/mkpwa.py` → `/home/claude/pwa/index.html` und `/mnt/user-data/outputs/gym-tracker/index.html`.
- Native App (Capacitor): `/home/claude/app` (www/index.html nach jedem Build kopieren: `cp /home/claude/pwa/index.html /home/claude/app/www/index.html`), Zip: `cd /home/claude && zip -qr GymApp.zip app -x 'app/node_modules/*'`.
- Basis: `/home/claude/backup_v8.html` + Module in Reihenfolge (build.py): lib.js+bimg.js, add9.js, ex.js, food.js, bar.js, feat.js, feat2.js, feat3.js, mig.js, imu.js, ord.js, rpe.js, rec.js, sleep.js, ah.js, inj.js, rd.js, bk.js, hk.js, pl.js. CSS: add9.css, neon.css.
- Speicherung: nur `localStorage` Schlüssel `gt4` (Objekt `S`). Kein Backend, keine Datenbank, kein Login, keine API-Aufrufe, keine Secrets.
- Tests: Playwright-Skripte in `/home/claude/qa` (hl, st, ov, rq, wk, rc, ah, ah3, ij, ijv, ord.test, rd, f5, cb, nat, bars, pl, perf). f5 nur mit `TZ=UTC` stabil.
- Bilder: `bimg.js` jetzt WebP (Original in `bimg.orig.js`).

## Phase 1 – Ergebnisse bisher
| Aufgabe | Status |
|---|---|
| Statische Analyse: 212 Button-Aktionen, alle haben Handler | ABGESCHLOSSEN |
| Ansichten erfasst: h, t, w, v, p, e, f, sl, ak, k, mv, r, u, ud, d, ei, fl, pl, x | ABGESCHLOSSEN |
| Sicherheit: keine fetch/XHR, keine API-Keys im Frontend | ABGESCHLOSSEN |
| Barrierefreiheit-Befund: kein Escape für Modals, keine :focus-visible-Stile, nur 16 aria-labels; prefers-reduced-motion teilweise vorhanden | ABGESCHLOSSEN (Befund) |
| Performance-Messung (700 Workouts): Start 0,34 s, Tabwechsel < 0,25 s | ABGESCHLOSSEN |
| Laufzeit-Crawl aller Tabs bei 320/390/768/1280 px (Fehler, Überlauf, Modals) – Skript `qa/crawl.js`: 0 Laufzeitfehler, 0 Konsolenfehler | ABGESCHLOSSEN |
| Überlauf 320 px im Profil (Jahres-Kachel „Schutztag genutzt“) – CSS `.ys>div{min-width:0}` | ABGESCHLOSSEN |
| Diagramm-Inventar (SVG): feat.js 21, add9.js 6, feat2.js 5, ex.js 3, ah/sleep/rec/food/feat3 je 1; Funktionen u. a. volChart, exChart, cmpChart, ahBars, ahLine, slLine, rpLine, folLine | ABGESCHLOSSEN |
| Umsetzungsplan | ABGESCHLOSSEN (siehe unten) |

## Phasen-Übersicht
| Phase | Status | Ergebnis |
|---|---|---|
| 1 Analyse | ABGESCHLOSSEN | siehe oben |
| 2 Designsystem | ABGESCHLOSSEN | Nutzer wählte „Vereinheitlichen“. Neu: tok.py (läuft im Build nach dem Zusammenbau, Original-CSS unverändert; Rücknahme: build.pre_tok.py). Tokens --r-xs/sm/md/lg/xl/pill (6/10/14/18/22/999), --sh-sm/md/lg (eigene Werte im Hellmodus), --t-fast/--t-med/--ease. Radien 36→24 Werte (Rest: Kreise/Spezialformen). Schriftgrößen-Ausreißer (9.5/10.5/12.5/14.5/19/25 px) gerundet, Abstände 3/5/7/9 px auf 4er-Raster. Primär- und Sekundärknöpfe jetzt gleiche Ecken (14 px) und gleiche Drück-Reaktion (scale .97); Karten und Hero-Karten einheitlich 22 px. Vorher/Nachher-Screens geprüft (Dunkel+Hell, Startseite+Training): keine Brüche. |
| 3 Animationen/Bedienbarkeit | ABGESCHLOSSEN | ux.js: Escape, Fokus-Management, role=dialog, aria-labels, :focus-visible, Disabled-State. Seitenübergänge (pgIn, Stagger, Slide) waren vorhanden und sind geprüft. |
| 4 Diagramme/Statistik | ABGESCHLOSSEN | Zeitfilter (qa/tf.js): Körpergewicht 30/90/Alles → 3 verschiedene Anzeigen, Aktivität 14/30/90 → 3, Sätze/Woche 7/28 → 2. 3D-Modell 7/28/90: färbt per WebGL, im Test-Browser nicht messbar (NICHT TESTBAR). |
| 4 (alt) | – | qa/nan.js: alle Tabs und Overlays mit 0 Daten, 1 Training sowie 120 Trainings/300 Tagen Health: kein NaN/undefined/Infinity in Text oder SVG, keine Fehler. Volumen in E2E rechnerisch geprüft (77,5×9=697,5 → 698 kg). NICHT einzeln geprüft: Tooltip-Inhalte jedes Diagramms, jeder Zeitfilter auf Datenänderung. |
| 5 Trainingssystem | ABGESCHLOSSEN | qa/e2e.js 12/12 OK, qa/wk.js OK. Training pausieren existiert nicht als Funktion. |
| 6 Daten/Sicherheit | ABGESCHLOSSEN | Kein Backend/Login (nicht anwendbar). Neu: Satz-Grenzen 600 kg / 200 Wdh (qa/val.js), doppelte Plannamen blockiert (qa/val2.js), XSS über Plannamen geprüft (sicher), Backup → Löschen → Wiederherstellen identisch, kaputte Datei wird abgelehnt ohne Datenverlust (qa/bkrt.js). Planfarbe im gespeicherten Wert unterscheidet sich nach Restore, sichtbar keine Auswirkung (Farbe kommt aus Akzentfarbe). |
| 7 Responsive/Performance/A11y | ABGESCHLOSSEN (teilweise) | Crawl 320/390/768/1280 px: 0 Fehler, 1 Überlauf (320 px Profil) behoben. Performance: Start 0,34 s, Tabwechsel <0,25 s bei 700 Workouts; Datei 4,2→2,1 MB. Kontrast gemessen: Text 17,7:1 (dunkel)/16,3:1 (hell), Nebentext 7,5:1/4,6:1 – erfüllt WCAG AA. Befund: weißer Text auf Akzentrot 3,65:1 (unter 4,5:1) – nicht geändert, da Markenfarbe; Nutzer entscheidet. Offen: Screenreader-Test auf echtem Gerät. |
| 8 Gesamttest | ABGESCHLOSSEN | 22 Testskripte + Crawl + NaN-Prüfung + Zeitfilter grün nach Design-Umstellung. |

## Testskripte (alle grün am 2026-11-20 ~01:00)
hl, st, ov, rq, wk, rc, ah, ah3, ij, ijv, ord.test, rd, cb, nat, bars, pl, ux, e2e, val, val2, bkrt, f5 (mit TZ=UTC), crawl, nan (E/O/F)

## Nicht testbar hier
- Echtes iPhone/Safari (Tastatur mit Komma, Haptik, PWA-Speicher), Health-Export echter Dateien, native App-Build (nur Workflow geschrieben).

## Umsetzungsplan (Ergebnis Phase 1)
Die App ist funktional stabil (0 Fehler im Crawl). Priorität daher: Bedienbarkeit und Feinschliff statt Neubau.
1. Phase 3 (zuerst, geringes Risiko): Escape schließt Overlays, Fokus springt ins Modal und zurück, sichtbarer Tastatur-Fokusring, globales prefers-reduced-motion, einheitliche Button-Press-Reaktion, Tab-Wechsel-Übergang prüfen.
2. Phase 4: jedes SVG-Diagramm mit Daten und ohne Daten rendern, Tooltips/Zeitfilter prüfen, Empty States.
3. Phase 5: End-to-End-Ablauf Plan → Training → Speichern → Startseite/Statistik → Verlauf als Test `qa/e2e.js`.
4. Phase 2: Design-Tokens konsolidieren (Radien, Abstände, Schatten) nur wo uneinheitlich; kein Rebranding ohne Rückfrage.
5. Phase 6: Eingabevalidierung (negative/absurde Gewichte, leere Namen), Datenmigration absichern, Backup-Wiederherstellung testen.
6. Phase 7: Responsive 320–1440, aria-labels für Icon-Buttons, Kontraste.
7. Phase 8: alle Tests, Abschlussbericht.

## Nicht anwendbar / Einschränkungen
- Backend, Datenbank, API, Authentifizierung, Benutzertrennung: existieren nicht (lokale App). Cloud-Freunde-Funktion (socSettings) separat prüfen.
- TypeScript/ESLint: Projekt ist reines JS ohne Toolchain; Netzwerk für npm aus → ESLint nicht installierbar. Ersatz: `node --check` auf Modulen.
- Tests nur im Chromium-Emulator, nicht auf echtem iPhone/Safari.

## Zuletzt bearbeitete Dateien
- p9/perf.js (rcModel-Cache), p9/pol.js (neu, nach perf.js in build.py), p9/sleep.js (aria-label), p9/add9.css; Tests qa/audit.js, home3.js, focus.js, lang.js, iso2.js, hh_*.js
- p9/add9.js (sanS, load), p9/p11.js, p9/perf.js (neu, nach p10.js in build.py), pwa/LOGIN_PLAN.md; Sicherung add9.pre_san.js
- p9/rel.js, p9/ijb.js, p9/p10.js (neu, in build.py nach pz.js), p9/imu.js (Undo-Text), /home/claude/mkpwa.py + csp.txt, pwa/manifest.webmanifest, pwa/sw.js, pwa/datenschutz.html, pwa/RELEASE.md, pwa/icon-maskable-512.png
- p9/pz.js (neu, in build.py nach ux.js), p9/add9.css (Knopf-Verlauf, Pause-Stile)
- p9/tok.py (neu), p9/build.py (ruft tok.apply), p9/build.pre_tok.py (Sicherung)
- p9/ux.js (Satz-Grenzen, doppelte Plannamen), qa/e2e.js, qa/val.js, qa/val2.js, qa/bkrt.js, qa/nan.js, qa/crawl.js
- p9/ux.js (neu, in build.py nach pl.js), p9/add9.css (Fokus, .ys-Fix)
- p9/pl.js (neu), p9/hk.js, p9/ah.js, p9/bk.js, p9/feat.js, p9/inj.js, p9/sleep.js, p9/add9.css, p9/bimg.js, p9/build.py, app/.github/workflows/*.yml

## AUFTRAG „ULTIMATE GYM APP TRANSFORMATION“ (21.11.2026)
Abgleich mit dem Auftrag: Phasen 1–10 der früheren Aufträge decken Analyse, Design-Tokens, Animationen, Training, Statistiken, PWA, Datenschutz und QA bereits ab. Neu bearbeitet:
| Phase | Status | Ergebnis |
|---|---|---|
| 1 Analyse | ABGESCHLOSSEN | Lücken: keine Fehler-Absicherung beim Rendern, Absturz bei beschädigten Daten, kein Equipment-Filter, keine Erkennung von Trainingspausen, Zahlformatierung langsam, Layout-Messungen erzwingen Reflow. Kein Login (lokale App). |
| 2 Stabilität | ABGESCHLOSSEN | add9.js: sanS() bereinigt Daten vor fixS (Trainings ohne Übungen, null-Einträge, kaputte Sätze/Pläne/Gewichte/Schlafwerte) – vorher Komplett-Absturz beim Start. load(): unlesbare Daten werden als gt4_broken gesichert (nie überschrieben), Hinweis mit Download; fehlende plans/w werden ergänzt statt alles zu verwerfen. p11.js: render() mit Fehler-Karte statt leerer Seite, globaler Fehler-Toast. Tests qa/crash.js, qa/broken.js, qa/p11.js. |
| 3 Performance | ABGESCHLOSSEN | perf.js: Intl.NumberFormat-Cache (nf, cuFmt), Nav-Indikator (lens) und Einblendeffekt (pgFx) auf nächsten Frame. Messprotokoll unten. |
| 4 Design | ABGESCHLOSSEN | bereits Token-System (tok.py); neue Elemente nutzen Tokens. |
| 5 Training | ABGESCHLOSSEN | qa/e2e2.js: Übung im laufenden Training hinzufügen, Pausentimer startet/zählt, Training übersteht Neuladen, Fortsetzen-Karte, doppeltes Abhaken stabil. |
| 6 Statistik/Intelligenz | ABGESCHLOSSEN | Hinweis „Willkommen zurück“ ab 7 Tagen Pause (echte Daten, ausblendbar, nicht bei Verletzung). Equipment-Filter (Langhantel, Kurzhantel, Maschine, Kabelzug, Smith, Körpergewicht) kombinierbar mit Muskelgruppe. |
| 7–8 Animation/Mobile/PWA | ABGESCHLOSSEN | unverändert gut (CLS 0). |
| 9 Sicherheit | IN BEARBEITUNG (BLOCKIERT) | Login/Sync geplant in pwa/LOGIN_PLAN.md (Firebase Spark empfohlen) – braucht Zustimmung + Konto des Nutzers. |
| 10–11 QA/Polish | ABGESCHLOSSEN | Regression 28 Skripte grün (p10/p11 nur unter file:// gelegentlich instabil, über HTTP 3/3 bzw. 6/6 grün). |

### Offene Punkte nachgeholt (21.11.2026, „mache alles“)
| Punkt | Status | Ergebnis |
|---|---|---|
| Startseite auf langsamen Geräten | ABGESCHLOSSEN | perf.js: rcModel zwischengespeichert (Schlüssel: Speicherzähler + Minute + Anzahl Trainings + Verletzungen; save() zählt hoch). 700 Trainings, CPU 4×: Startseite 562–649 ms → 378–435 ms, längste Interaktion 520 → ~330 ms. |
| Visueller Audit | ABGESCHLOSSEN | 10 Ansichten × Dunkel/Hell × leer/voll (qa/audit.js, Kontaktbögen /tmp/sheet_*.png): 0 Fehler, Leer-Zustände vorhanden. Befunde behoben: bis zu 5 Erinnerungskarten gestapelt → pol.js zeigt nur die wichtigste (Backup > Health > Größe > Foto) + „N weitere Hinweise anzeigen“; Tagesform-Karte war unter „Aktivität“ gerutscht → wieder direkt unter „Als Nächstes“. 0 DOM-Änderungen im Leerlauf (keine Schleife). |
| Fokus/Tastatur | ABGESCHLOSSEN | qa/focus.js: 38 Tab-Stopps, alle mit sichtbarem Fokusring und Namen. |
| Lint | ABGESCHLOSSEN | ESLint (Regeln für echte Fehler) auf dem gebauten Code: 23 Meldungen, 0 echte Fehler (abgesicherte window.claude-Zugriffe, B3 aus eigenem Skriptblock, absichtliche NaN-Prüfung). TypeScript-Prüfung nicht sinnvoll (reines JS ohne Typen); Lighthouse nicht installiert → Kennzahlen per PerformanceObserver. |
| Sprache/Formate | ABGESCHLOSSEN | qa/lang.js über alle Tabs und Overlays: keine englischen Begriffe, keine Punkt-Dezimalzahlen, kein AM/PM. Gefunden: Schlafbalken mit Screenreader-Text „2026-11-08“ → jetzt „Sonntag, 8. November: 7 Std.“. Entscheidung: nur Deutsch, keine Übersetzungsschicht (einziger Nutzer deutschsprachig; halbe Übersetzungen verbietet der Auftrag). |
| 320-px-Befund aus dem Crawl | ABGESCHLOSSEN | Ursache gefunden: im laufenden Training lief die Reihe „+ Satz | + Aufwärmsatz | Auto-Aufwärmen“ über den Rand, und lange Übungsnamen lagen unter „0/3“. Fix: Knopfreihen umbrechen, Übungskopf auf Handys als Raster (Bild | Name, darunter Satzzähler und Pausenzeit). 320 und 390 px ohne Überlauf, Screenshots geprüft. |
| Optisches Redesign „Quiet Luxury“ | BEWUSST NICHT | Nutzer wählte am 20.11. „Vereinheitlichen“; Token-System umgesetzt. |
| Login/Sync | BLOCKIERT | wartet auf Firebase-Konto + Zustimmung. |

### Performance-Protokoll (Playwright über lokalen HTTP-Server, 390×844, gemessen 21.11.2026)
| Szenario | Laden | LCP | CLS | längste Interaktion | Tabwechsel (ms) |
|---|---|---|---|---|---|
| Vorher: leer, schnell | 188 ms | 828 ms | 0 | 96 ms | t146 v102 p153 ak94 h100 |
| Vorher: leer, CPU 4× | 772 ms | 1500 ms | 0 | 312 ms | t421 v193 p380 ak252 h203 |
| Vorher: 700 Trainings, CPU 4× | 1089 ms | 1772 ms | 0 | 456 ms | t369 v442 p565 ak469 h562 |
| Nachher: leer, CPU 4× | 638 ms | 1308 ms | 0 | 216 ms | t399 v183 p274 ak279 h215 |
| Nachher: 700 Trainings, CPU 4× | 997 ms | 1736 ms | 0 | 520 ms | t417 v253 p469 ak283 h649 |
Profiler (Startseite 8×): lens/cache 229 ms → nicht mehr messbar. Messungen schwanken ±20 % zwischen Läufen. Budget: LCP < 2,5 s (erreicht), CLS < 0,1 (erreicht), Interaktion < 200 ms (erreicht auf schnellem Gerät; bei 4× gedrosselter CPU und 700 Trainings NICHT erreicht). Verbleibender Engpass: Erholungsmodell rcModel (~15 ms je Startseiten-Aufbau) und Gesamt-DOM-Aufbau der Startseite. Lighthouse nicht installierbar (kein Netzwerk für npm).

## PHASE 9 – Release (ABGESCHLOSSEN, 21.11.2026)
- Datenfluss-Audit: keine Netzwerkaufrufe; Cloud/Freunde/KI nur in der Claude-Version (window.claude), auf GitHub Pages inaktiv.
- Neu rel.js: „Alle Daten löschen“ (Bestätigung LÖSCHEN, löscht gt*-Schlüssel und Caches), Datenschutz-Fenster, navigator.storage.persist(), Offline/Online-Hinweis.
- mkpwa.py: Content-Security-Policy (connect-src 'self', keine Fremdquellen) + no-referrer; Datei /home/claude/csp.txt.
- PWA: manifest mit id/lang/description/categories, separates maskierbares Icon (pwa/icon-maskable-512.png), sw.js Cache gym-v2 inkl. datenschutz.html.
- pwa/datenschutz.html: Entwurf mit Platzhaltern + Impressum-Vorlage (keine Rechtsberatung).
- pwa/RELEASE.md: Datenflüsse, Rechtliche Punkte, Hosting-Vergleich (GitHub Pages/Cloudflare/Netlify, geprüft 21.11.2026), Tests, Schritt-für-Schritt-Launch, Rollback.
- Tests über echten HTTP-Server (qa/rel.js, h_*.js): SW aktiv, offline starten/anlegen/behalten, 0 externe Requests, 0 CSP-Verstöße, e2e/Backup/Löschen grün.
- Verletzungen auf Körpermodellen (Nutzerwunsch): ijb.js – 2D-Karte orange schraffiert, 3D-Modell orange + Legende „Verletzt“ (qa/ijb.js).

## PHASE 10 – Fitness-Funktionen (ABGESCHLOSSEN)
Prioritätenliste (Bestand geprüft):
| # | Funktion | Status |
|---|---|---|
| 8 | Undo bei versehentlichem Löschen | NEU (p10.js): Schnappschuss vor löschenden Aktionen (Training, Plan, Satz, Übung, Gewicht, Verletzung, Maße, Abbruch) → Rückgängig-Leiste. Getestet. |
| 6 | Körpermaße | NEU: 6 Maße, Datum, Prüfung 10–250 cm, Verlauf mit Löschen, Mini-Kurven und Veränderung. Getestet. |
| 7 | Favoriten | NEU: Stern in der Übungsbibliothek, Favoriten-Gruppe oben. Vorlagen (Pläne + Satz-Vorgaben S.tpl) existierten. Getestet. |
| 1 | Pausentimer mit Ton/Vibration/individuell | vorhanden (S.sig, S.rx je Übung) |
| 2 | Bestleistungen | vorhanden (prMap, „Neuer Rekord“) |
| 3 | Übungsbibliothek mit Muskeln/Medien | vorhanden (Gruppen, Bilder, Detail) |
| 4 | Kalender/Historie | vorhanden (Verlauf, Jahres-Heatmap) |
| 5 | Fortschrittsanalyse | vorhanden (Volumen, Sätze/Woche, Muskelverteilung, 3D) |
| 9 | Ziele | vorhanden (goals, Wochenziel-Ring) |
| 10 | Vorschläge nächste Einheit | vorhanden (nextPlan, „Letztes Mal“-Vorschlag, Tagesform) |
Hinweis Test: qa/p10.js ist unter file:// gelegentlich instabil (Chromium-Speicher beim Neuladen), über HTTP 6/6 stabil (qa/h_p10.js).

## ABSCHLUSSBERICHT (2026-11-20)

### Erfolgreich getestet (Chromium-Emulator)
- Alle Tabs (Startseite, Training, Essen, Schlaf, Aktivität, Verlauf, Profil, Einstellungen) bei 320/390/768/1280 px: 0 Laufzeit- und Konsolenfehler.
- Alle Diagramme/Statistiken mit 0, 1 und vielen Daten: kein NaN/undefined/Infinity.
- Kompletter Nutzerablauf (12 Schritte) inkl. Volumenberechnung und Persistenz nach Neuladen.
- Zeitfilter Körpergewicht, Aktivität, Sätze/Woche.
- Eingabegrenzen, doppelte Plannamen, XSS über Plannamen, Backup-Rundlauf, kaputte Backup-Datei.
- Escape/Fokus für Overlays inkl. 3D-Modell (Fehler gefunden und behoben).
- Performance: Start 0,34 s, Tabwechsel <0,25 s bei 700 Trainings; Datei 4,2→2,1 MB.

### Behobene Fehler
1. Doppelte Plannamen möglich → Trainings falsch zugeordnet (behoben).
2. Unplausible Satzwerte (5000 kg, 999 Wdh) gespeichert (Grenze 600 kg/200 Wdh).
3. 3D-Modell und Overlays nicht per Escape schließbar, kein Fokus-Management (behoben).
4. Überlauf Profil-Kachel bei 320 px (behoben).
5. Uneinheitliche Ecken (36 Werte, Primär-/Sekundärknopf verschieden) → Token-System.

### Implementiert, aber nicht vollständig getestet
- Fokus-/Screenreader-Verhalten auf echtem iPhone (VoiceOver).
- Schriftgrößen-Einstellung auf iOS Safari (text-size-adjust).
- Native App (Capacitor-Workflow, Health-Sync, Mitteilungen): nur mit simuliertem Capacitor getestet.

### Nicht testbar hier
- Backend/Datenbank/Auth/Benutzertrennung: existieren nicht (lokale App).
- TypeScript/ESLint: kein Toolchain, Netzwerk für npm aus.
- WebGL-3D-Modell-Einfärbung, echte Health-Exporte, Safari-spezifisches Verhalten.

### Noch offen / Entscheidung des Nutzers
- ERLEDIGT 2026-11-20: Knopf-Kontrast – Primärknopf pro Akzentfarbe abgedunkelt (pz.js, --bk1/--bk2): Rot 4,86, Blau 4,90, Grün 4,95, Orange 4,78, Lila 4,70 (alle ≥ 4,5:1).
- ERLEDIGT 21.11.: 320-px-Knopf-Überlauf im laufenden Training behoben.
- ERLEDIGT 2026-11-20: Training pausieren (pz.js): Knopf neben „Training beenden“, Hinweiskarte, Uhr eingefroren (auch nach Neuladen), Pausenzeit zählt nicht zur Dauer. Test qa/pz.js grün.

## Nächste exakte Schritte
1. Auf Zustimmung zum Login warten (LOGIN_PLAN.md). Ohne Zustimmung nichts mit externen Konten bauen.
2. Optional Performance: rcModel memoisieren (Schlüssel S.ts + Minute), Startseite in zwei Etappen rendern.
3. Nach jedem Build: `python3 build.py && python3 /home/claude/mkpwa.py && cp /home/claude/pwa/index.html /home/claude/app/www/index.html`; Tests über HTTP: `cd /home/claude/pwa && python3 -m http.server 8765` und qa/h_*.js.

## Nächste exakte Schritte (nur falls gewünscht)
1. /tmp/crawl2.txt prüfen (Crawl nach Token-Umstellung; sonst `cd /home/claude/qa && timeout 900 node crawl.js`).
2. Phase 4 Rest: Zeitfilter je Diagramm prüfen (ändern sich die Daten?) – Skript qa/tf.js anlegen.
3. Abschlussbericht (Phase 8) in diese Datei.
Nach jedem Build: `python3 build.py && python3 /home/claude/mkpwa.py && cp /home/claude/pwa/index.html /home/claude/app/www/index.html`.
