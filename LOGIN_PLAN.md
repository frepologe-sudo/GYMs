# Plan: Login und Sync zwischen Geräten (noch NICHT umgesetzt)

Stand: 21.11.2026. Wird erst nach deiner Zustimmung gebaut, weil ein externes Konto nötig ist und Trainings-/Gesundheitsdaten dann auf fremden Servern liegen.

## Warum überhaupt
Heute liegen alle Daten nur im Browser. Ein Login bringt: gleiche Daten auf Handy und PC, kein Verlust bei Handywechsel, automatisches Cloud-Backup.

## Anbieter-Vergleich (Bedingungen geprüft am 21.11.2026)
| | Firebase (Spark, kostenlos) | Supabase (Free) |
|---|---|---|
| Kosten | 0 €, keine Zahlungsmethode nötig | 0 € |
| Nutzerkonten | 50.000 aktive Nutzer/Monat | 50.000 aktive Nutzer/Monat |
| Datenbank | Firestore 1 GiB, 10 GiB Datenverkehr/Monat | 500 MB pro Projekt, 5 GB Datenverkehr |
| Haken | Bei Überschreitung muss man auf den kostenpflichtigen Blaze-Plan wechseln | Kostenlose Projekte werden nach 1 Woche Inaktivität pausiert und müssen dann manuell im Dashboard fortgesetzt werden; keine automatischen Backups |
| Bezahlstufe | Blaze: nutzungsabhängig | Pro: ab 25 $/Monat |

**Empfehlung: Firebase Spark** mit Datenstandort Europa (z. B. Frankfurt). Grund: kein Pausieren nach Inaktivität, größeres Datenkontingent, keine Kreditkarte nötig. Supabase wäre die Alternative, wenn du lieber eine klassische SQL-Datenbank willst.

## Wie es funktionieren würde
1. **Offline zuerst bleibt:** Die App speichert wie heute sofort lokal. Sync läuft im Hintergrund, wenn Internet da ist.
2. **Anmeldung:** E-Mail-Link (ohne Passwort) oder „Mit Apple/Google anmelden“. Passwörter verwaltet der Anbieter, nie die App.
3. **Datenmodell:** `users/{uid}/workouts/{id}`, `users/{uid}/plans/{name}`, `users/{uid}/meta/settings` usw. – je Training ein Dokument (Firestore erlaubt max. 1 MiB pro Dokument, deshalb nicht alles in eins).
4. **Benutzertrennung:** Sicherheitsregel „nur `request.auth.uid == uid` darf lesen/schreiben“. Wird mit dem Firebase-Emulator getestet, bevor etwas live geht.
5. **Konflikte:** Jeder Eintrag bekommt `updatedAt`. Bei gleichzeitiger Änderung gewinnt die neueste Version pro Eintrag (nicht pro Gesamtstand). Löschungen werden als „gelöscht“-Markierung synchronisiert, damit ein altes Gerät gelöschte Trainings nicht wiederbelebt.
6. **Erster Login:** Lokale Daten werden hochgeladen; liegen schon Daten in der Cloud, fragt die App „zusammenführen“ oder „Cloud übernehmen“ – mit Backup vorher.
7. **Konto löschen:** löscht alle Cloud-Dokumente und das Konto, lokale Daten optional.
8. **Schlüssel:** Die Firebase-Web-Konfiguration ist öffentlich und kein Geheimnis; Schutz entsteht allein durch die Sicherheitsregeln. Keine geheimen Schlüssel im Frontend.

## Datenschutz-Folgen
- Gesundheitsdaten (Art. 9 DSGVO) liegen dann bei Google → ausdrückliche Einwilligung beim Login nötig, Datenschutzerklärung erweitern, Auftragsverarbeitungsvertrag mit Google abschließen, Region EU wählen.
- Ohne Login bleibt die App wie heute komplett lokal – der Login wäre optional.
- Vor öffentlichem Launch mit Login: rechtliche Prüfung dringend empfohlen.

## Was du tun müsstest
1. Firebase-Konto mit deinem Google-Konto anlegen (console.firebase.google.com), Projekt erstellen, Region Europa.
2. Authentication (E-Mail-Link, optional Apple) und Firestore aktivieren.
3. Mir die Web-Konfiguration (apiKey, projectId usw.) geben.
4. Sagen, ob Login optional oder Pflicht sein soll.

## Aufwand und Risiko
- Umsetzung in mehreren Schritten: Anmeldung → Hochladen → Sync mit Konflikten → Konto löschen. Jeder Schritt einzeln getestet.
- Risiko: Datenverlust bei fehlerhaftem Sync → deshalb vor jedem ersten Sync automatisches lokales Backup und Rückgängig.
- Laufende Kosten: 0 €, solange die Spark-Limits nicht überschritten werden; bei Überschreitung muss man aktiv auf Blaze wechseln (kein automatisches Abbuchen im Spark-Plan).
