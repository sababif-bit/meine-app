# meine-app

Eine kleine Python-Anwendung mit automatisierter CI/CD-Pipeline über GitHub Actions.

## CI/CD-Pipeline

Die Pipeline wird bei einem Push auf `main` oder bei einem Pull Request gestartet.

Sie führt unter anderem folgende Schritte aus:

- Repository auschecken
- Python 3.12 einrichten
- Dependencies aus `requirements.txt` installieren
- Tests mit pytest ausführen
- Anwendung als `app.zip` paketieren
- Build als Artifact speichern
- Artifact im Deployment-Job herunterladen
- Deployment nur nach erfolgreichen Tests durchführen
- Production-Environment mit Required Review verwenden
- GitHub Release automatisch erstellen

Das Deployment wird nur auf dem Branch `main` ausgeführt und ist vom erfolgreichen Test-Job abhängig.

## Secrets und Production Environment

Der `DEPLOY_TOKEN` ist als Environment Secret im Environment `production` gespeichert.

Dadurch ist der Secret nicht im normalen Test-Job verfügbar, sondern nur im Deployment-Job, der das Production-Environment verwendet.

Das Production-Environment ist zusätzlich durch einen Required Reviewer geschützt.

## Security Challenge – Tag 4

| Problem | Risiko / verletzte Regel | Lösung |
|---|---|---|
| `permissions: write-all` | Verletzt das Least-Privilege-Prinzip. Der Workflow erhält mehr Rechte als nötig. | Standardmäßig nur `contents: read` verwenden und zusätzliche Rechte nur für den benötigten Job vergeben. |
| `actions/checkout@main` | Die Action ist nicht auf eine feste Version festgelegt. Der ausgeführte Action-Code kann sich ändern. | Eine feste Version verwenden, z. B. `actions/checkout@v4`. |
| `pull_request_target` zusammen mit dem Checkout von `pull_request.head.sha` | Nicht vertrauenswürdiger Code aus einem Pull Request kann in einem privilegierten Workflow ausgecheckt und potenziell ausgeführt werden. Dadurch können Secrets oder andere Rechte gefährdet werden. | Unvertrauenswürdigen PR-Code nicht in einem privilegierten `pull_request_target`-Workflow ausführen. Für normalen PR-Code `pull_request` verwenden. |
| Secret wird in `token.txt` geschrieben und als Artifact hochgeladen | Der Secret wird in einer Datei gespeichert und kann dadurch offengelegt werden. | Secrets niemals in Dateien schreiben oder als Artifact hochladen. |
| Deployment ohne `needs: test` | Das Deployment ist nicht vom erfolgreichen Test abhängig. Fehlerhafter Code könnte deployed werden. | `needs: test` verwenden. |
| Keine Beschränkung auf `main` | Das Deployment könnte auch für unerwünschte Git-Refs ausgeführt werden. | `if: github.ref == 'refs/heads/main'` verwenden. |
| Kein `environment: production` | Schutzregeln wie Required Reviewers und Environment Secrets werden nicht angewendet. | `environment: production` verwenden und das Environment entsprechend schützen. |