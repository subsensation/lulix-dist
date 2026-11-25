# Lulix — pacchetti Windows

Qui ci sono soltanto i pacchetti compilati di Lulix, con la loro impronta.
Il codice vive in un repository privato.

## Scarica

Il pacchetto non sta fra i file di questo repository: è allegato alla release.

- Installer nativo r12, un solo eseguibile senza PowerShell: https://github.com/subsensation/lulix-dist/releases/download/v0.1.0-r12/lulix-setup-r11.exe
- Pagina delle release: https://github.com/subsensation/lulix-dist/releases/latest
- Pacchetto zip r11 con installa.ps1: https://github.com/subsensation/lulix-dist/releases/download/v0.1.0-r11/lulix-win-dist-r11.zip

## Ultima versione

| Versione | File | Byte | SHA-256 |
|---|---|---|---|
| r11 | lulix-win-dist-r11.zip | 88.960.057 | 8a19ecbab4e646298998c9705cdfbd6d38faec7c02e279a64e3cfdb677947345 |

## Installazione

Scarica lo zip della versione che ti serve dalla pagina Releases, poi in PowerShell:

    Expand-Archive lulix-win-dist-r11.zip -DestinationPath lulix-r11
    cd lulix-r11
    powershell -ExecutionPolicy Bypass -File .\installa.ps1 -Pacchetto ..\lulix-win-dist-r11.zip -Sha256Atteso 8a19ecbab4e646298998c9705cdfbd6d38faec7c02e279a64e3cfdb677947345

L'installazione è per-utente: non serve l'amministratore e non tocca la cartella di sistema.

Poi, in una finestra nuova:

    lulix setup
    lulix connect setup
    lulix connect pair

`setup` chiede provider e chiave; `connect pair` stampa il codice a sei cifre da
inserire nella web app dal telefono.

## Verifica

Ogni pacchetto porta la sua impronta qui sopra e nel titolo della release.
Prima di installare confrontala:

    Get-FileHash lulix-win-dist-r11.zip -Algorithm SHA256
