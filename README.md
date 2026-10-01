# meine-app

Eine kleine Python-Anwendung mit automatisierter CI-Pipeline über GitHub Actions.

## CI-Pipeline

Die Pipeline wird automatisch bei einem Push oder Pull Request gestartet.

Sie führt folgende Schritte aus:

- Repository auschecken
- Python installieren
- Dependencies aus `requirements.txt` installieren
- Tests mit pytest ausführen
- Testergebnisse als Artifact speichern

Die Tests werden über eine Matrix mit mehreren Python-Versionen ausgeführt:

- Python 3.10
- Python 3.11
- Python 3.12

Bei einem fehlgeschlagenen Test wird die Pipeline als fehlgeschlagen markiert. Fehler können über die Logs in GitHub Actions analysiert werden.