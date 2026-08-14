# DSO Actions Live Demo

Dieses Repository ist eine kleine, kommentierte Live-Demo für den Einstieg in [GitHub Actions](https://docs.github.com/de/actions).

## Repository-Beschreibung

Anhand von acht übersichtlichen Workflows zeigt dieses Repository, wie GitHub Actions auf einen Push reagieren oder manuell gestartet werden können. Die Beispiele führen bewusst nur einfache Befehle aus: So bleibt sichtbar, wie Trigger, Jobs, Runner, Schritte, Eingaben und GitHub-Kontextvariablen zusammenspielen.

Dabei werden folgende Grundlagen demonstriert:

- automatischer Start eines Workflows bei einem Push auf `main`
- manueller Start über die GitHub-Actions-Oberfläche
- Ausführung eines Jobs auf einem von GitHub bereitgestellten Ubuntu-Runner
- Verwendung einer fertigen Action mit `uses`
- Ausführung eines Shell-Befehls mit `run`
- Zugriff auf Runner-Informationen und manuelle Eingaben mit `${{ ... }}`
- parallele Jobs und gemeinsame Folgejobs mit `needs`
- feste Pipeline-Phasen in der Reihenfolge Lint, Build, Test und Deploy
- Variablen auf Workflow-, Job- und Step-Ebene
- Übergabe kleiner Werte zwischen Jobs mit Outputs
- Übertragung von Dateien zwischen Jobs mit Artifacts

## Inhaltsverzeichnis

- [Schnellstart](#schnellstart)
  - [Voraussetzungen](#voraussetzungen)
  - [Automatische Workflows starten](#automatische-workflows-starten)
  - [Manuellen Workflow starten](#manuellen-workflow-starten)
- [Repository-Struktur](#repository-struktur)
- [Workflows](#workflows)
  - [Starter CI](#starter-ci)
  - [Test Runner](#test-runner)
  - [Manual Run Demo](#manual-run-demo)
  - [Jobs verbinden](#jobs-verbinden)
  - [Pipeline-Phasenreihenfolge](#pipeline-phasenreihenfolge)
  - [Umgebungsvariablen](#umgebungsvariablen)
  - [Werte zwischen Jobs weitergeben](#werte-zwischen-jobs-weitergeben)
  - [Artefakte zwischen Jobs übertragen](#artefakte-zwischen-jobs-übertragen)

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

Die fünf zusätzlichen Beispiele `Job Dependencies Demo`, `Ordered Pipeline Demo`, `Environment Variables Demo`, `Job Outputs Demo` und `Artifacts Demo` werden auf dieselbe Weise manuell gestartet. Öffne nach der Ausführung einen Workflow-Lauf, um seine Job-Abhängigkeiten und Ausgaben zu untersuchen.

## Repository-Struktur

Die wichtigsten Projektdateien sind folgendermaßen organisiert:

- `.github/workflows/`
  - `ci.yml`: Automatischer Beispiel-Workflow mit dem Anzeigenamen `Starter CI`.
  - `test.yml`: Zweiter automatischer Beispiel-Workflow mit dem Anzeigenamen `Test Runner`.
  - `manual.yml`: Manuell startbarer Workflow mit einer Eingabe für die Zielumgebung.
  - `job-dependencies.yml`: Verbindet zwei parallele Jobs mit einem gemeinsamen Folgejob.
  - `ordered-pipeline.yml`: Bildet eine feste Pipeline-Reihenfolge mit vier Jobs ab.
  - `environment-variables.yml`: Zeigt die Gültigkeitsbereiche von Umgebungsvariablen.
  - `job-outputs.yml`: Übergibt einen kleinen Textwert von einem Job an einen Folgejob.
  - `artifacts.yml`: Überträgt eine Datei zwischen zwei Jobs.
- `Readme.md`: Beschreibt Zweck, Aufbau und Verwendung der Demo.

## Workflows

### Starter CI

`ci.yml` startet bei jedem Push auf `main`. Der Job `build` verwendet `actions/checkout`, um den Repository-Inhalt auf den Runner zu laden. Danach gibt ein Shell-Befehl den Wert der GitHub-Kontextvariable `runner.os` aus.

### Test Runner

`test.yml` demonstriert denselben grundlegenden Ablauf wie `ci.yml`. Der separate Workflow eignet sich dazu, in der Actions-Oberfläche zu sehen, dass ein Ereignis mehrere Workflows auslösen kann.

### Manual Run Demo

`manual.yml` verwendet den Trigger `workflow_dispatch`. Beim Start kann über das Eingabefeld `environments` eine Zielumgebung angegeben werden. Der Ausdruck `${{ github.event.inputs.environments }}` setzt diesen Wert in den ausgeführten Shell-Befehl ein.

### Jobs verbinden

`job-dependencies.yml` enthält die Jobs `build`, `test` und `deploy`. Da `build` und `test` keine Abhängigkeit besitzen, können sie parallel laufen. Der Job `deploy` verwendet `needs: [build, test]` und startet erst, wenn beide vorherigen Jobs erfolgreich waren:

```text
build ──┐
        ├──> deploy
test ───┘
```

Schlägt `build` oder `test` fehl, wird `deploy` standardmäßig übersprungen. Die Job-IDs in `needs` müssen exakt mit den IDs unter `jobs` übereinstimmen.

### Pipeline-Phasenreihenfolge

GitHub Actions besitzt keine eigene `stages`-Eigenschaft. In `ordered-pipeline.yml` wird die gewünschte Reihenfolge deshalb als Kette von Job-Abhängigkeiten modelliert:

```text
lint ──> build ──> test ──> deploy
```

Jeder Job verwendet `needs`, um auf seinen direkten Vorgänger zu warten. Schlägt eine frühere Phase fehl, werden die davon abhängigen späteren Phasen standardmäßig nicht ausgeführt. Dadurch gelangt beispielsweise kein fehlgeschlagener Build in die Test- oder Deployment-Phase.

### Umgebungsvariablen

`environment-variables.yml` zeigt drei Gültigkeitsbereiche für Variablen. Eine Variable unter dem obersten `env` gilt im gesamten Workflow, eine Variable unter `jobs.<job-id>.env` nur im jeweiligen Job und eine Variable direkt am Step nur in diesem Step.

Da der Workflow auf Ubuntu läuft, werden die Werte im Shell-Befehl beispielsweise mit `$APP_NAME` gelesen. Die Ausgabe zeigt, dass alle drei Variablen im letzten Step verfügbar sind.

### Werte zwischen Jobs weitergeben

`job-outputs.yml` erzeugt im ersten Job einen kleinen Textwert. Der Step schreibt ihn in die von GitHub bereitgestellte Datei `$GITHUB_OUTPUT`. Anschließend wird der Step-Output als Job-Output veröffentlicht.

Der zweite Job wartet mit `needs: create-message` auf den ersten Job und liest den Wert über `${{ needs.create-message.outputs.greeting }}`. Outputs eignen sich für kleine Werte wie eine Versionsnummer oder einen Status, nicht für ganze Dateien.

### Artefakte zwischen Jobs übertragen

`artifacts.yml` erzeugt im ersten Job eine Textdatei und lädt sie mit `actions/upload-artifact` hoch. Der zweite Job läuft auf einer neuen Runner-VM, lädt die Datei mit `actions/download-artifact` herunter und zeigt ihren Inhalt an:

```text
Datei erstellen ──> Artifact hochladen ──> Artifact herunterladen ──> Datei lesen
```

Artifacts eignen sich beispielsweise für Build-Ergebnisse, Testberichte oder Logdateien. Das erzeugte Artifact kann außerdem über die Zusammenfassung des Workflow-Laufs heruntergeladen werden.
