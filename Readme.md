# DSO Actions Live Demo

Dieses Repository zeigt verschiedene GitHub-Actions-Beispiele für den Einstieg in CI/CD. Es ist so aufgebaut, dass es als kleines, öffentliches Referenzprojekt für Workflows, Triggers, Jobs und Deployment-Szenarien genutzt werden kann.

## Überblick

GitHub führt Workflows nur aus Dateien direkt in `.github/workflows/` aus – Unterordner werden dort ignoriert. Damit dieser Ordner trotzdem übersichtlich bleibt, liegen dort nur zwei Dateien:

- `starter-ci.yml` – der einfachste mögliche Workflow (Push → Checkout → `echo`)
- `repo-tour.yml` – ein kleiner Bonus-Workflow, der spielerisch durchs Repository führt

Alle weiteren Beispiele sind reines Lesematerial und liegen als echte Unterordner unter [`examples/`](examples/), sortiert nach dem, was sie zeigen:

1. **basics** – weitere Trigger (`push`, `workflow_dispatch`)
2. **jobs** – Abhängigkeiten, Reihenfolge, Variablen, Outputs, Artifacts
3. **ci** – CI-Pipelines für ein echtes Node.js- und Python-Projekt
4. **deploy** – echte Auslieferung per FTP und SSH mit Secrets

Diese Beispiele lassen sich nicht in diesem leeren Repository starten (sie setzen ein echtes Projekt oder echte Secrets voraus), sind aber vollständige, kommentierte YAML-Dateien zum Nachlesen und Übernehmen in eigene Projekte.

## Inhaltsverzeichnis

- [Schnellstart](#schnellstart)
  - [Voraussetzungen](#voraussetzungen)
  - [Starter CI ausprobieren](#starter-ci-ausprobieren)
  - [Repo Tour manuell starten](#repo-tour-manuell-starten)
- [Repository-Struktur](#repository-struktur)
- [Lauffähige Workflows](#lauffähige-workflows)
  - [Starter CI](#starter-ci)
  - [Repo Tour](#repo-tour)
- [Referenz-Beispiele (`examples/`)](#referenz-beispiele-examples)
  - [Basics](#basics)
    - [Test Runner](#test-runner)
    - [Manual Run Demo](#manual-run-demo)
  - [Jobs & Datenaustausch](#jobs--datenaustausch)
    - [Jobs verbinden](#jobs-verbinden)
    - [Pipeline-Phasenreihenfolge](#pipeline-phasenreihenfolge)
    - [Umgebungsvariablen](#umgebungsvariablen)
    - [Werte zwischen Jobs weitergeben](#werte-zwischen-jobs-weitergeben)
    - [Artefakte zwischen Jobs übertragen](#artefakte-zwischen-jobs-übertragen)
  - [CI für echte Projekte](#ci-für-echte-projekte)
    - [Frontend CI – Node.js](#frontend-ci--nodejs)
    - [Backend CI – Python](#backend-ci--python)
  - [Deployment](#deployment)
    - [FTP-Deploy – statische Website live schieben](#ftp-deploy--statische-website-live-schieben)
    - [Angular Build & Deploy](#angular-build--deploy)
- [Mitwirken](#mitwirken)
- [Gesamtziel](#gesamtziel)
- [Lizenz](#lizenz)

## Schnellstart

### Voraussetzungen

- ein GitHub-Konto
- dieses Projekt als GitHub-Repository
- aktivierte GitHub Actions im Repository
- Schreibzugriff auf das Repository, wenn ein Workflow gestartet oder eine Änderung gepusht werden soll

Für diese Demo müssen keine Pakete installiert und keine Anwendung lokal gestartet werden. Die Workflows laufen vollständig auf GitHub.

### Starter CI ausprobieren

`Starter CI` reagiert auf einen Push auf den Branch `main`.

1. Ändere eine Datei im Repository.
2. Erstelle einen Commit mit der Änderung.
3. Pushe den Commit auf `main`.
4. Öffne im GitHub-Repository den Bereich **Actions**.
5. Wähle den Workflow-Lauf aus, um seine Jobs, Schritte und Ausgaben anzusehen.

Der Workflow lädt das Repository auf einen Ubuntu-Runner und gibt anschließend das Betriebssystem des Runners aus.

### Repo Tour manuell starten

`Repo Tour` wird nicht durch einen Push ausgelöst, sondern über die GitHub-Oberfläche:

1. Öffne im Repository den Bereich **Actions**.
2. Wähle links **Repo Tour** aus.
3. Klicke auf **Run workflow**.
4. Öffne nach der Ausführung den Workflow-Lauf und lies dir die Ausgabe durch.

> **Hinweis:** Ein manuell startbarer Workflow wird in GitHub normalerweise erst angezeigt, nachdem seine Workflow-Datei auf dem Standardbranch vorhanden ist.

## Repository-Struktur

Die wichtigsten Projektdateien sind folgendermaßen organisiert:

- `.github/workflows/` – läuft wirklich, flach (GitHub führt nur von hier aus)
  - `starter-ci.yml`: Einfachster Workflow – Push, Checkout, eine `echo`-Ausgabe.
  - `repo-tour.yml`: Führt einmal quer durchs Repository – der beste erste Workflow zum Ausprobieren.
- `examples/` – nur zum Nachlesen und Übernehmen, echte Unterordner nach Kategorie
  - **`basics/`**
    - `test-runner.yml`: Zweiter automatischer Beispiel-Workflow, zeigt, dass ein Ereignis mehrere Workflows auslösen kann.
    - `manual-run.yml`: Manuell startbarer Workflow mit einer Eingabe für die Zielumgebung.
  - **`jobs/`**
    - `dependencies.yml`: Verbindet zwei parallele Jobs mit einem gemeinsamen Folgejob.
    - `ordered-pipeline.yml`: Bildet eine feste Pipeline-Reihenfolge mit vier Jobs ab.
    - `environment-variables.yml`: Zeigt die Gültigkeitsbereiche von Umgebungsvariablen.
    - `outputs.yml`: Übergibt einen kleinen Textwert von einem Job an einen Folgejob.
    - `artifacts.yml`: Überträgt eine Datei zwischen zwei Jobs.
  - **`ci/`**
    - `frontend.yml`: CI-Workflow für ein Node.js-Frontend mit Install, Test und Build.
    - `backend.yml`: CI-Workflow für ein Python-Backend mit Install, Test und pytest.
  - **`deploy/`**
    - `ftp.yml`: Praxisbeispiel für das Hochladen einer statischen HTML/CSS-Website per FTP mit GitHub Secrets.
    - `angular.yml`: Build, Test und Deployment einer Angular-App per SSH/SCP.
- `LICENSE`: MIT-Lizenz – der Code darf frei zum Lernen, Kopieren und Anpassen verwendet werden.
- `Readme.md`: Beschreibt Zweck, Aufbau und Verwendung der Demo.

## Lauffähige Workflows

Diese beiden Workflows liegen in `.github/workflows/` und lassen sich in diesem Repository wirklich ausführen.

### Starter CI

`starter-ci.yml` startet bei jedem Push auf `main`. Der Job `build` verwendet `actions/checkout`, um den Repository-Inhalt auf den Runner zu laden. Danach gibt ein Shell-Befehl den Wert der GitHub-Kontextvariable `runner.os` aus.

### Repo Tour

`repo-tour.yml` braucht kein Secret, kein Projekt und geht garantiert nicht rot: Er checkt das Repository aus, gibt eine Kopfzeile aus, zählt die lauffähigen Workflows unter `.github/workflows/`, blättert dann durch die Referenz-Kategorien unter `examples/` und schließt mit einem zufälligen GitHub-Actions-Tipp.

## Referenz-Beispiele (`examples/`)

Alles ab hier liegt unter `examples/`, ist reines Lesematerial und taucht im Actions-Tab von GitHub nicht auf – Unterordner werden von GitHub Actions nicht ausgeführt. Die Dateien sind vollständig und kommentiert, damit sie sich 1:1 in ein eigenes Projekt kopieren lassen.

### Basics

#### Test Runner

`examples/basics/test-runner.yml` demonstriert denselben grundlegenden Ablauf wie `starter-ci.yml`. Der separate Workflow eignet sich dazu, in der Actions-Oberfläche zu sehen, dass ein Ereignis mehrere Workflows auslösen kann.

#### Manual Run Demo

`examples/basics/manual-run.yml` verwendet den Trigger `workflow_dispatch`. Beim Start kann über das Eingabefeld `environments` eine Zielumgebung angegeben werden. Der Ausdruck `${{ github.event.inputs.environments }}` setzt diesen Wert in den ausgeführten Shell-Befehl ein.

### Jobs & Datenaustausch

#### Jobs verbinden

`examples/jobs/dependencies.yml` enthält die Jobs `build`, `test` und `deploy`. Da `build` und `test` keine Abhängigkeit besitzen, können sie parallel laufen. Der Job `deploy` verwendet `needs: [build, test]` und startet erst, wenn beide vorherigen Jobs erfolgreich waren:

```text
build ──┐
        ├──> deploy
test ───┘
```

Schlägt `build` oder `test` fehl, wird `deploy` standardmäßig übersprungen. Die Job-IDs in `needs` müssen exakt mit den IDs unter `jobs` übereinstimmen.

#### Pipeline-Phasenreihenfolge

GitHub Actions besitzt keine eigene `stages`-Eigenschaft. In `examples/jobs/ordered-pipeline.yml` wird die gewünschte Reihenfolge deshalb als Kette von Job-Abhängigkeiten modelliert:

```text
lint ──> build ──> test ──> deploy
```

Jeder Job verwendet `needs`, um auf seinen direkten Vorgänger zu warten. Schlägt eine frühere Phase fehl, werden die davon abhängigen späteren Phasen standardmäßig nicht ausgeführt. Dadurch gelangt beispielsweise kein fehlgeschlagener Build in die Test- oder Deployment-Phase.

#### Umgebungsvariablen

`examples/jobs/environment-variables.yml` zeigt drei Gültigkeitsbereiche für Variablen. Eine Variable unter dem obersten `env` gilt im gesamten Workflow, eine Variable unter `jobs.<job-id>.env` nur im jeweiligen Job und eine Variable direkt am Step nur in diesem Step.

Da der Workflow auf Ubuntu läuft, werden die Werte im Shell-Befehl beispielsweise mit `$APP_NAME` gelesen. Die Ausgabe zeigt, dass alle drei Variablen im letzten Step verfügbar sind.

#### Werte zwischen Jobs weitergeben

`examples/jobs/outputs.yml` erzeugt im ersten Job einen kleinen Textwert. Der Step schreibt ihn in die von GitHub bereitgestellte Datei `$GITHUB_OUTPUT`. Anschließend wird der Step-Output als Job-Output veröffentlicht.

Der zweite Job wartet mit `needs: create-message` auf den ersten Job und liest den Wert über `${{ needs.create-message.outputs.greeting }}`. Outputs eignen sich für kleine Werte wie eine Versionsnummer oder einen Status, nicht für ganze Dateien.

#### Artefakte zwischen Jobs übertragen

`examples/jobs/artifacts.yml` erzeugt im ersten Job eine Textdatei und lädt sie mit `actions/upload-artifact` hoch. Der zweite Job läuft auf einer neuen Runner-VM, lädt die Datei mit `actions/download-artifact` herunter und zeigt ihren Inhalt an:

```text
Datei erstellen ──> Artifact hochladen ──> Artifact herunterladen ──> Datei lesen
```

Artifacts eignen sich beispielsweise für Build-Ergebnisse, Testberichte oder Logdateien.

### CI für echte Projekte

#### Frontend CI – Node.js

`examples/ci/frontend.yml` zeigt den typischen CI-Ablauf für ein Frontend-Projekt: Repository auschecken, Node.js installieren, Abhängigkeiten installieren, Tests ausführen und anschließend den Build prüfen.

#### Backend CI – Python

`examples/ci/backend.yml` demonstriert denselben Ansatz für Python-Projekte. Nach dem Setup von Python werden die Abhängigkeiten installiert und anschließend mit `pytest` die Tests gestartet.

### Deployment

#### FTP-Deploy – statische Website live schieben

`examples/deploy/ftp.yml` zeigt ein reales Deployment-Szenario für eine einfache HTML/CSS-Seite. Beim Push auf `main` wird der Inhalt aus einem lokalen Ordner hochgeladen und per FTP auf einen externen Webserver verschoben.

Wichtig dabei ist die Trennung von Code und Zugangsdaten:

- Server, Benutzername und Passwort stehen nicht im Workflow-Code
- Sie werden als GitHub Secrets abgespeichert
- GitHub setzt diese Werte nur zur Laufzeit in den Job ein

#### Angular Build & Deploy

`examples/deploy/angular.yml` zeigt einen etwas anderen Ablauf: Zuerst werden Abhängigkeiten installiert, Tests und Build ausgeführt, danach wird der fertige Build als Artifact weitergereicht und per SSH auf einen Server übertragen.

Die wichtigsten Grundprinzipien dabei sind:

- separate Jobs für Build/Test und Deployment
- `needs` verhindert einen Deploy, wenn der Build oder Test fehlschlägt
- `upload-artifact` und `download-artifact` transportieren das Ergebnis zwischen den Jobs
- Secrets schützen die Serverzugänge vor dem Code

## Mitwirken

Dieses Repository ist öffentlich und steht unter der MIT-Lizenz. Fehlerkorrekturen,
verständlichere Erklärungen und neue kleine Beispiele sind ausdrücklich willkommen.

Du brauchst dafür keinen direkten Schreibzugriff: Über **Fork → eigener Branch →
Pull Request** kannst du eine Änderung vorschlagen. Kleine Textkorrekturen lassen
sich sogar vollständig über die GitHub-Webseite erledigen. Die ausführliche
Schritt-für-Schritt-Anleitung steht in [CONTRIBUTING.md](CONTRIBUTING.md).

Vor einem Beitrag helfen außerdem diese Dokumente:

- [Verhaltenskodex](CODE_OF_CONDUCT.md)
- [Sicherheitsrichtlinie](SECURITY.md)
- die Vorlagen, die GitHub beim Erstellen eines Issues oder Pull Requests anzeigt

## Gesamtziel

Dieses Repository soll als kompakte Referenz für GitHub Actions dienen. Es zeigt die wichtigsten Bausteine einer CI/CD-Pipeline auf verständliche Weise:

- Trigger
- Jobs
- Steps
- Uses
- Run
- Abhängigkeiten zwischen Jobs
- Secrets
- Deployment-Beispiele

Damit lässt sich das Grundprinzip von GitHub Actions gut erklären: Automatisierung von Code-Checks, Tests und Deployment mit klaren, nachvollziehbaren Schritten.

## Lizenz

Dieses Repository steht unter der [MIT-Lizenz](LICENSE). Der Code darf frei zum Lernen, Kopieren und Anpassen verwendet werden.
