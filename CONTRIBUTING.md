# Zu diesem Projekt beitragen

Danke, dass du dieses Referenzprojekt verbessern möchtest. Beiträge sind auch
ohne Schreibzugriff auf das ursprüngliche Repository möglich: Du arbeitest in
deinem eigenen Fork und schlägst deine Änderung anschließend mit einem Pull
Request vor.

## Der einfache Weg über die GitHub-Webseite

Für kleine Korrekturen brauchst du Git nicht lokal zu installieren:

1. Öffne die Datei, die du ändern möchtest, auf GitHub.
2. Klicke auf das Stiftsymbol **Edit this file**.
3. GitHub erstellt bei Bedarf automatisch einen Fork unter deinem Konto.
4. Ändere die Datei und beschreibe kurz, was du verbessert hast.
5. Klicke auf **Propose changes** und erstelle danach den Pull Request.

## Lokal mit einem Fork arbeiten

Mit der GitHub CLI lässt sich ein Fork in einem Schritt erstellen und klonen:

```bash
gh repo fork FriggemannMichael/gh-actions-workflow --clone
cd gh-actions-workflow
git switch -c docs/kurze-beschreibung
```

Ohne GitHub CLI kannst du das Repository zuerst über die GitHub-Webseite
forken und anschließend deinen Fork klonen:

```bash
git clone https://github.com/DEIN-NAME/gh-actions-workflow.git
cd gh-actions-workflow
git remote add upstream https://github.com/FriggemannMichael/gh-actions-workflow.git
git switch -c docs/kurze-beschreibung
```

Dabei bezeichnet `origin` deinen eigenen Fork und `upstream` dieses
ursprüngliche Repository.

## Eine Änderung einreichen

1. Erstelle einen eigenen Branch, zum Beispiel `docs/ftp-erklaerung`,
   `feature/neues-beispiel` oder `fix/yaml-fehler`.
2. Nimm eine kleine, klar abgegrenzte Änderung vor.
3. Prüfe Einrückung, Kommentare und Links. YAML-Dateien verwenden Leerzeichen,
   keine Tabulatoren.
4. Erstelle einen verständlichen Commit und pushe den Branch in deinen Fork:

   ```bash
   git add PFAD/ZUR/DATEI
   git commit -m "Kurze Beschreibung der Änderung"
   git push -u origin HEAD
   ```

5. Öffne auf GitHub einen Pull Request von deinem Fork nach
   `FriggemannMichael/gh-actions-workflow:main`.
6. Beantworte Rückfragen oder Verbesserungsvorschläge direkt im Pull Request.

Pull Requests führen automatisch den sicheren Beispiel-Workflow aus. Läufe
von neuen externen Beitragenden müssen gegebenenfalls erst durch einen
Maintainer freigegeben werden.

## Was gut zum Projekt passt

- verständlichere Erklärungen für Einsteiger
- korrigierte oder modernisierte GitHub-Actions-Beispiele
- neue, kleine Beispiele mit deutlichen Kommentaren
- Fehlerkorrekturen in YAML, Markdown oder Links
- Verbesserungen an Sicherheit und Barrierefreiheit der Dokumentation

Bitte eröffne für größere Umbauten zuerst ein Issue. So lässt sich gemeinsam
klären, ob die Idee zum Lernziel und zur Struktur des Projekts passt.

## Regeln für Beispiele

- Beispiele sollen einen klaren Lernzweck haben und möglichst klein bleiben.
- Zugangsdaten, Tokens, private URLs und andere Secrets gehören niemals in
  einen Commit.
- Externe Actions sollen mit Anbieter, Name und Version verständlich erklärt
  werden.
- Neue ausführbare Workflows gehören nur nach `.github/workflows/`. Reine
  Referenzbeispiele bleiben unter `examples/`, damit sie nicht automatisch
  ausgeführt werden.
- Mit einem Beitrag akzeptierst du, dass er unter der MIT-Lizenz des Projekts
  veröffentlicht wird.

Bitte beachte außerdem den [Verhaltenskodex](CODE_OF_CONDUCT.md) und melde
Sicherheitsprobleme gemäß der [Sicherheitsrichtlinie](SECURITY.md).
