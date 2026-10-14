# Netboard

Gaestefinder fuer Netzwerkabende. Pro Abend ein Thema, ca. 14 Plaetze, ca. 10 % Zusagequote, also ca. 140 Kandidaten.
Inhaber des Projekts: Eljakim. Vault-Notiz: `Business/Netboard.md`.

## Start
Eine Datei: `index.html`, Doppelklick reicht. Daten in localStorage (Schluessel `netboard_daten`).
Vorschau im Chat: Server `netboard` in `~/Downloads/.claude/launch.json` zeigt auf eine Kopie im Scratchpad, vor jedem Test neu kopieren.

## Aufbau
- Datenmodell: events, personen, einladungen (Status mit Verlauf und Ergebnis), verbindungen (wer hat wen kennengelernt), clubs, firmen.
- Speicher steckt in `speicher.laden/sichern`. Fuer mehrere Nutzer durch Supabase ersetzen.
- Scoring ohne KI: Thema, Beruf, Netzwerk, Region kommen aus der Recherche-Datei. Runde und Zusagen rechnet die App.
  Unbekannte Werte zaehlen nicht mit. Zusagequote erst ab 3 eigenen bzw. 8 Rollen-Einladungen.
- Matching `paar()`: gemeinsame Themen, ergaenzende Rollen, Branche, Ort, fruehere Verbindungen. Jede Verbindung mit Gruenden.
- "Welche Person fehlt": gewuenschte Rollen im Event gegen Rollen in der Runde.
- Suche laeuft nur ueber den Chat-Skill. Die API-Suche im Browser wurde am 03.10.2026 entfernt (Schluessel im Browser unsicher, nie genutzt). Kommt als Supabase-Funktion zurueck, wenn ein Kunde zahlt. Alter Stand: Git-Tag `vor-entschlacken`.
- Alternativ Import als JSON: `{"personen":[{name, position, firma, branche, ort, website, rolle, tags[], profile[{art,url}], werte{thema,beruf,netzwerk,region}, begruendung, einladungsgrund, quellen[{text,url}]}]}`.
- Die App verschickt nichts. Einladungstexte werden kopiert.

## Regeln
- Keine Aussage ohne Quelle, unbekannt bleibt leer.
- Kein automatisches LinkedIn-Auslesen. Google-Suche und oeffentliche Quellen, LinkedIn nur zum Pruefen einzelner Profile.

## Recherche heute
Laeuft ueber Claude im Chat mit dem Skill `/netboard <Thema> <Anzahl>` (`~/.claude/skills/netboard/SKILL.md`). Ergebnis als JSON in `~/Downloads/netboard/recherche/`, in der App "Datei importieren". Der App-Knopf zeigt den fertigen Auftrag zum Kopieren. Eigene API erst, wenn IFBA zahlt.

## GitHub
Repo: https://github.com/netboard-app/netboard-app.github.io (oeffentlich, Organisation netboard-app). Live: https://netboard-app.github.io/
Hochladen: `git push` (gh-CLI unter ~/bin/gh, angemeldet als Eljas-Webstar). Keine echten Personendaten ins Repo, die Recherche-Dateien liegen ausserhalb in `~/Downloads/netboard/recherche/`.

## Anmeldung
Sichtschutz im Browser: Benutzername und Passwort werden als SHA-256 von `benutzername-klein:passwort` mit `ZUGANG` in index.html verglichen, gemerkt in localStorage `netboard_zugang`. Kein echter Schutz, weil der Code oeffentlich ist. Echte Anmeldung kommt mit Supabase. Zugangsdaten stehen nicht im Repo.

## Netzwerk-Funktionen (03.10.2026)
- Netzwerk hat drei Bereiche: Kontakte (Themen nur aus Events, Sortierung Wichtigkeit/Letzter Kontakt/Name), Wer hilft? (`helferSuchen`, Wortsuche mit Liste `VERWANDT` und `FUELLWOERTER`), Kennenlernen (`netzKennen`). Tueroeffner-Tab entfernt, `kamUeber` steht nur noch im Profil.
- Wichtigkeit: `autoWichtigkeit` aus beruf/netzwerk, wertvoll, Geschaeftskontakt, Teilnahmen, Empfehlungen; von Hand ueberschreibbar (`p.wichtigkeit` 1 bis 3).
- Kontaktpflege: `p.kontakte` [{datum, art, notiz}] plus Events als Kontakt. Startseite "Lange nicht gemeldet": sehr wichtig nach 60, wichtig nach 120 Tagen, nur wer schon Kontakt hatte.
- Steckbrief am Profil: zuletzt, woher, worueber reden, gemeinsame Kontakte.
- Wer hilft? ist Wortsuche ohne KI. Wenn Treffer fehlen, zuerst `VERWANDT` erweitern. Allgemeine Silben wie "bau" oder "genossenschaft" vermeiden, sie treffen Falsches (Baumwerk, Jagdgenossenschaften).
- Kontaktwege (03.10.2026): `p.kontaktwege` [{art: Telefon|E-Mail|Kontaktformular|Nachricht, wert, typ, quelle}]. Karte zeigt "erreichbar" oder "kein Kontaktweg" und den ersten Weg, Filter "Nur erreichbare" im Kandidaten-Tab, Block "So erreichst du …" im Profil und Steckbrief. Telefon als tel:-Link plus Kopieren. Hinweis: E-Mail erst nach erstem Kontakt (UWG).

## Entschlackt (03.10.2026)
- App ist neutral: keine Firmen-, Loge- oder Themenbezuege in Beispiel, Platzhaltern, Texten. Nicht wieder einbauen, der Code ist oeffentlich.
- Event: "Wen suchen wir?" (Typen) ist gleichzeitig Rollen am Tisch (`e.rollen = e.typen`). Branchen stecken in Stichworten.
- Einladung: zwei Vorlagen, `e.einladungstext` (lang, E-Mail) und `e.kurztext` (Direktnachricht). Umschalter `S.textArt`.
- Runde: "Liste fuer die Einladerin" (`einladerListe`) als PDF (Druckfenster) oder CSV (Semikolon, BOM, fuer Excel).

## Schwerpunkt Gesundheit und Pflege (10.10.2026)
- Zweiter Schwerpunkt neben Entscheidern: Betriebe mit taeglichem Kontakt zu Senioren und Menschen mit Pflegegrad (Reha-Sport, Sanitaetshaus, Hausnotruf, Seniorenwohnen, Wohnungsgenossenschaften, Heilberufe). Eigenes Thema "Gesundheit und Pflege", Regeln und Suchmuster im Skill `netboard`.
- Typ-Vorschlaege: Schluessel `gesund` in `TYP_VORSCHLAEGE`, nur Berufsbezeichnungen.
- Heilberufe: Feld `heilberuf` (Import in `uebernehmen`), Hinweis ueber `heilHinweis()` an Karte und Profil. Text neutral lassen, Code ist oeffentlich.
- Testlauf: `~/Downloads/netboard/recherche/gesundheit-und-pflege-2026-10-10.json`, 10 Personen, 7 Typen, alle erreichbar.
- Social-Pruefung per Websuche lehnt Namenssuchen auf Profilseiten inzwischen oft ab. Profile dann von Hand pruefen lassen.

## Clubs (14.10.2026)
- Zweck: Firmen aus dem Umfeld eines Gegners (Partner, Sponsoren, Gremien) bewerten und zum Heimspiel einladen, Gegeneinladung beim Rueckspiel. Hintergrund steht in der Vault-Notiz, nicht hier (Datei ist oeffentlich).
- Vierter Knopf unten "Clubs". `D.clubs` [{id, name, land, stadion, stand, spiele[{datum, wettbewerb, heim, quelle}]}], `D.firmen` [{id, clubId, name, branche, ort, land, website, verbindung, verbindungText, nutzen, nutzenGrund, kooperationsidee, quellen[], personId}].
- Ansprechpartner ist eine normale Person, Verknuepfung nur ueber `firma.personId` (`firmenVonPerson`, `firmaFuer`). Keine zweite Kopie an der Person.
- Import: `clubUebernehmen()` erkennt Club-Dateien an `firmen`, auch im Event-Import. Gleicher Club und gleiche Firma (Name) werden aktualisiert. Personen ueber `personUebernehmen()` (aus `uebernehmen` herausgeloest).
- Event mit `clubId`: `clubVorschlaege()` legt die Ansprechpartner als Vorschlaege an, beim Speichern und bei jedem Import. Kandidatenkarte zeigt Verbindung und Idee.
- Druckliste: `clubListe()` nach Muster `einladerListe()`, sortiert nach Nutzen. "Event anlegen" nimmt das naechste Heimspiel (`naechstesSpiel` bevorzugt `heim`).
- Amtstraeger: Feld `amtstraeger`, Hinweis `amtsHinweis()` an Karte, Profil und Druckliste, wie `heilberuf`.
- Neutral: Bereich heisst "Clubs", App erzeugt `/netboard club <Club> 15`. Keine Anlass- oder Kundenbezuege in Code und in diese Datei, beides ist oeffentlich.
- Testlauf: erste Club-Datei in `~/Downloads/netboard/recherche/club-*.json`, 15 Firmen, 13 mit Ansprechpartner.
- Vorschau-Test ohne Anmeldung: in der Scratchpad-Kopie `angemeldet()` auf `true` setzen, nie im Repo.
