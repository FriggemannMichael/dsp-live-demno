# DSO Actions Live Demo

Dieses Repository zeigt verschiedene GitHub-Actions-Beispiele für den Einstieg in CI/CD. Es ist so aufgebaut, dass es als kleines, öffentliches Referenzprojekt für Workflows, Triggers, Jobs und Deployment-Szenarien genutzt werden kann.

## Überblick

Das Projekt enthält mehrere einfache, aber realistische Beispiele für GitHub Actions. Die Workflows zeigen, wie automatisch bei einem Push auf `main` Aufgaben ausgeführt werden, wie Jobs voneinander abhängen und wie reale Deployments mit GitHub Secrets abgesichert werden.

Die Beispiele sind in vier Kategorien gruppiert und bauen aufeinander auf:

1. **Basics** – Trigger, Runner, `uses` und `run`
2. **Jobs & Datenaustausch** – Abhängigkeiten, Reihenfolge, Variablen, Outputs, Artifacts
3. **CI für echte Projekte** – Node.js- und Python-Pipelines
4. **Deployment** – echte Auslieferung per FTP und SSH mit Secrets

Alle Workflow-Dateien in `.github/workflows/` tragen deshalb ein Präfix (`basics-`, `jobs-`, `ci-`, `deploy-`), damit die Kategorie auch beim Durchblättern des Ordners auf GitHub sofort erkennbar ist.

## Inhaltsverzeichnis

- [Schnellstart](#schnellstart)
  - [Voraussetzungen](#voraussetzungen)
  - [Automatische Workflows starten](#automatische-workflows-starten)
  - [Manuellen Workflow starten](#manuellen-workflow-starten)
- [Repository-Struktur](#repository-struktur)
- [Workflows](#workflows)
  - [Basics: Trigger & Grundlagen](#basics-trigger--grundlagen)
    - [Starter CI](#starter-ci)
    - [Test Runner](#test-runner)
    - [Manual Run Demo](#manual-run-demo)
  - [Jobs, Abhängigkeiten & Datenaustausch](#jobs-abhängigkeiten--datenaustausch)
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
- [Lizenz](#lizenz)

## Schnellstart

### Voraussetzungen

- ein GitHub-Konto
- dieses Projekt als GitHub-Repository
- aktivierte GitHub Actions im Repository
- Schreibzugriff auf das Repository, wenn ein Workflow gestartet oder eine Änderung gepusht werden soll

Für diese Demo müssen keine Pakete installiert und keine Anwendung lokal gestartet werden. Die Workflows laufen vollständig auf GitHub.

### Automatische Workflows starten

Die Workflows `Starter CI` und `Test Runner` reagieren auf einen Push auf den Branch `main`.

1. Ändere eine Datei im Repository.
2. Erstelle einen Commit mit der Änderung.
3. Pushe den Commit auf `main`.
4. Öffne im GitHub-Repository den Bereich **Actions**.
5. Wähle einen Workflow-Lauf aus, um seine Jobs, Schritte und Ausgaben anzusehen.

Beide Workflows laden das Repository auf einen Ubuntu-Runner und geben anschließend das Betriebssystem des Runners aus. Da sie denselben Trigger verwenden, werden bei einem Push auf `main` beide Workflows gestartet.

### Manuellen Workflow starten

Der Workflow `Manual Run Demo` wird nicht durch einen Push ausgelöst, sondern über die GitHub-Oberfläche:

1. Öffne im Repository den Bereich **Actions**.
2. Wähle links **Manual Run Demo** aus.
3. Klicke auf **Run workflow**.
4. Gib die gewünschte Zielumgebung ein. Ohne Änderung wird `staging` verwendet.
5. Starte den Workflow und öffne anschließend die Ausgabe des Jobs `deploy`.

Der Job zeigt die ausgewählte Umgebung lediglich mit `echo` an. Er führt kein echtes Deployment durch.

> **Hinweis:** Ein manuell startbarer Workflow wird in GitHub normalerweise erst angezeigt, nachdem seine Workflow-Datei auf dem Standardbranch vorhanden ist.

Die zusätzlichen Beispiele `Job Dependencies Demo`, `Ordered Pipeline Demo`, `Environment Variables Demo`, `Job Outputs Demo` und `Artifacts Demo` werden auf dieselbe Weise manuell gestartet. Öffne nach der Ausführung einen Workflow-Lauf, um seine Job-Abhängigkeiten und Ausgaben zu untersuchen.

## Repository-Struktur

Die wichtigsten Projektdateien sind folgendermaßen organisiert:

- `.github/workflows/`
  - **Basics**
    - `basics-starter-ci.yml`: Automatischer Beispiel-Workflow mit dem Anzeigenamen `Starter CI`.
    - `basics-test-runner.yml`: Zweiter automatischer Beispiel-Workflow mit dem Anzeigenamen `Test Runner`.
    - `basics-manual-run.yml`: Manuell startbarer Workflow mit einer Eingabe für die Zielumgebung.
  - **Jobs & Datenaustausch**
    - `jobs-dependencies.yml`: Verbindet zwei parallele Jobs mit einem gemeinsamen Folgejob.
    - `jobs-ordered-pipeline.yml`: Bildet eine feste Pipeline-Reihenfolge mit vier Jobs ab.
    - `jobs-environment-variables.yml`: Zeigt die Gültigkeitsbereiche von Umgebungsvariablen.
    - `jobs-outputs.yml`: Übergibt einen kleinen Textwert von einem Job an einen Folgejob.
    - `jobs-artifacts.yml`: Überträgt eine Datei zwischen zwei Jobs.
  - **CI für echte Projekte**
    - `ci-frontend.yml`: CI-Workflow für ein Node.js-Frontend mit Install, Test und Build.
    - `ci-backend.yml`: CI-Workflow für ein Python-Backend mit Install, Test und pytest.
  - **Deployment**
    - `deploy-ftp.yml`: Praxisbeispiel für das Hochladen einer statischen HTML/CSS-Website per FTP mit GitHub Secrets.
    - `deploy-angular.yml`: Build, Test und Deployment einer Angular-App per SSH/SCP.
- `LICENSE`: MIT-Lizenz – der Code darf frei zum Lernen, Kopieren und Anpassen verwendet werden.
- `Readme.md`: Beschreibt Zweck, Aufbau und Verwendung der Demo.

## Workflows

### Basics: Trigger & Grundlagen

#### Starter CI

`basics-starter-ci.yml` startet bei jedem Push auf `main`. Der Job `build` verwendet `actions/checkout`, um den Repository-Inhalt auf den Runner zu laden. Danach gibt ein Shell-Befehl den Wert der GitHub-Kontextvariable `runner.os` aus.

#### Test Runner

`basics-test-runner.yml` demonstriert denselben grundlegenden Ablauf wie `basics-starter-ci.yml`. Der separate Workflow eignet sich dazu, in der Actions-Oberfläche zu sehen, dass ein Ereignis mehrere Workflows auslösen kann.

#### Manual Run Demo

`basics-manual-run.yml` verwendet den Trigger `workflow_dispatch`. Beim Start kann über das Eingabefeld `environments` eine Zielumgebung angegeben werden. Der Ausdruck `${{ github.event.inputs.environments }}` setzt diesen Wert in den ausgeführten Shell-Befehl ein.

### Jobs, Abhängigkeiten & Datenaustausch

#### Jobs verbinden

`jobs-dependencies.yml` enthält die Jobs `build`, `test` und `deploy`. Da `build` und `test` keine Abhängigkeit besitzen, können sie parallel laufen. Der Job `deploy` verwendet `needs: [build, test]` und startet erst, wenn beide vorherigen Jobs erfolgreich waren:

```text
build ──┐
        ├──> deploy
test ───┘
```

Schlägt `build` oder `test` fehl, wird `deploy` standardmäßig übersprungen. Die Job-IDs in `needs` müssen exakt mit den IDs unter `jobs` übereinstimmen.

#### Pipeline-Phasenreihenfolge

GitHub Actions besitzt keine eigene `stages`-Eigenschaft. In `jobs-ordered-pipeline.yml` wird die gewünschte Reihenfolge deshalb als Kette von Job-Abhängigkeiten modelliert:

```text
lint ──> build ──> test ──> deploy
```

Jeder Job verwendet `needs`, um auf seinen direkten Vorgänger zu warten. Schlägt eine frühere Phase fehl, werden die davon abhängigen späteren Phasen standardmäßig nicht ausgeführt. Dadurch gelangt beispielsweise kein fehlgeschlagener Build in die Test- oder Deployment-Phase.

#### Umgebungsvariablen

`jobs-environment-variables.yml` zeigt drei Gültigkeitsbereiche für Variablen. Eine Variable unter dem obersten `env` gilt im gesamten Workflow, eine Variable unter `jobs.<job-id>.env` nur im jeweiligen Job und eine Variable direkt am Step nur in diesem Step.

Da der Workflow auf Ubuntu läuft, werden die Werte im Shell-Befehl beispielsweise mit `$APP_NAME` gelesen. Die Ausgabe zeigt, dass alle drei Variablen im letzten Step verfügbar sind.

#### Werte zwischen Jobs weitergeben

`jobs-outputs.yml` erzeugt im ersten Job einen kleinen Textwert. Der Step schreibt ihn in die von GitHub bereitgestellte Datei `$GITHUB_OUTPUT`. Anschließend wird der Step-Output als Job-Output veröffentlicht.

Der zweite Job wartet mit `needs: create-message` auf den ersten Job und liest den Wert über `${{ needs.create-message.outputs.greeting }}`. Outputs eignen sich für kleine Werte wie eine Versionsnummer oder einen Status, nicht für ganze Dateien.

#### Artefakte zwischen Jobs übertragen

`jobs-artifacts.yml` erzeugt im ersten Job eine Textdatei und lädt sie mit `actions/upload-artifact` hoch. Der zweite Job läuft auf einer neuen Runner-VM, lädt die Datei mit `actions/download-artifact` herunter und zeigt ihren Inhalt an:

```text
Datei erstellen ──> Artifact hochladen ──> Artifact herunterladen ──> Datei lesen
```

Artifacts eignen sich beispielsweise für Build-Ergebnisse, Testberichte oder Logdateien. Das erzeugte Artifact kann außerdem über die Zusammenfassung des Workflow-Laufs heruntergeladen werden.

### CI für echte Projekte

#### Frontend CI – Node.js

`ci-frontend.yml` zeigt den typischen CI-Ablauf für ein Frontend-Projekt: Repository auschecken, Node.js installieren, Abhängigkeiten installieren, Tests ausführen und anschließend den Build prüfen.

Das ist ein klassischer Einstieg, weil damit das Grundprinzip von CI sehr klar wird: Bei jedem Push wird der Code automatisch überprüft, bevor er weiterverarbeitet wird.

#### Backend CI – Python

`ci-backend.yml` demonstriert denselben Ansatz für Python-Projekte. Nach dem Setup von Python werden die Abhängigkeiten installiert und anschließend mit `pytest` die Tests gestartet.

Dieses Beispiel eignet sich gut, um zu zeigen, dass GitHub Actions unabhängig von der Programmiersprache funktioniert.

### Deployment

#### FTP-Deploy – statische Website live schieben

`deploy-ftp.yml` zeigt ein reales Deployment-Szenario für eine einfache HTML/CSS-Seite. Beim Push auf `main` wird der Inhalt aus einem lokalen Ordner hochgeladen und per FTP auf einen externen Webserver verschoben.

Wichtig dabei ist die Trennung von Code und Zugangsdaten:

- Server, Benutzername und Passwort stehen nicht im Workflow-Code
- Sie werden als GitHub Secrets abgespeichert
- GitHub setzt diese Werte nur zur Laufzeit in den Job ein

#### Angular Build & Deploy

`deploy-angular.yml` zeigt einen etwas anderen Ablauf: Zuerst werden Abhängigkeiten installiert, Tests und Build ausgeführt, danach wird der fertige Build als Artifact weitergereicht und per SSH auf einen Server übertragen.

Die wichtigsten Grundprinzipien dabei sind:

- separate Jobs für Build/Test und Deployment
- `needs` verhindert einen Deploy, wenn der Build oder Test fehlschlägt
- `upload-artifact` und `download-artifact` transportieren das Ergebnis zwischen den Jobs
- Secrets schützen die Serverzugänge vor dem Code

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
