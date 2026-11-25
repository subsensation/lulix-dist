# Lulix — pacchetti Windows

Qui ci sono soltanto i pacchetti compilati di Lulix, con la loro impronta.
Il codice vive in un repository privato.

## Scarica

Il pacchetto non sta fra i file di questo repository: è allegato alla release.

- Installer nativo r13, con diagnostica integrata: https://github.com/subsensation/lulix-dist/releases/download/v0.1.0-r13/lulix-setup-r13.exe
- Pagina delle release: https://github.com/subsensation/lulix-dist/releases/latest
- Se Windows blocca l'eseguibile: [DIAGNOSI_BLOCCO_WINDOWS.md](DIAGNOSI_BLOCCO_WINDOWS.md)
- Pacchetto zip r11 con installa.ps1: https://github.com/subsensation/lulix-dist/releases/download/v0.1.0-r11/lulix-win-dist-r11.zip

## Installazione su Windows, passo per passo

L'installazione è per-utente: non serve l'amministratore e non tocca cartelle
di sistema. Non si scarica dal browser: Edge e Chrome applicano SmartScreen e,
se l'avviso non viene confermato, il file viene cancellato. Si scarica da
PowerShell, che non passa da quel filtro.

Apri PowerShell (menu Start, scrivi PowerShell) e incolla i comandi uno alla
volta.

### 1. Vai nella cartella dei download

    cd $env:USERPROFILE\Downloads

### 2. Scarica l'installer

    Invoke-WebRequest https://github.com/subsensation/lulix-dist/releases/download/v0.1.0-r13/lulix-setup-r13.exe -OutFile lulix-setup.exe

### 3. Verifica l'impronta

    Get-FileHash .\lulix-setup.exe -Algorithm SHA256

Deve risultare:

    99EE2E1348FB33485DB2690610F1921F0D967C857C278792E290A368B995B7A2

e la dimensione 57.661.987 byte. Se non combacia, fermati e non eseguirlo.

### 4. Togli il marchio «scaricato da internet»

    Unblock-File .\lulix-setup.exe

Serve perché Windows marca i file arrivati dalla rete e le policy di controllo
delle applicazioni possono rifiutarli. Senza questo passo l'installazione può
fermarsi con «un criterio di controllo dell'applicazione ha bloccato il file».

### 5. Esegui l'installer

    .\lulix-setup.exe

Si apre la finestra di installazione: Avanti, Installa, Fine. Alla fine
propone di configurare Lulix: accetta.

Se compare l'avviso di Windows «Windows ha protetto il PC», scegli
**Ulteriori informazioni** e poi **Esegui comunque**: il programma non è
firmato digitalmente, e questo è l'unico modo finché non lo sarà.

### 6. Configura il modello

In una finestra PowerShell nuova (il PATH è stato appena cambiato):

    lulix setup

Chiede provider, chiave e modello. Per OpenRouter il modello è
`deepseek/deepseek-v4.1-flash-1017`.

### 7. Collega il telefono

    lulix connect setup
    lulix connect pair

Il secondo comando stampa un indirizzo del tipo `http://192.168.1.20:8733` e un
codice a sei cifre. Dal telefono, sulla stessa rete Wi-Fi del PC, apri
l'indirizzo e inserisci il codice.

### Cosa fa e cosa non fa

- installa in `%LOCALAPPDATA%\Programs\Lulix`, senza privilegi di amministratore
- aggiunge `lulix` al PATH dell'utente e crea le voci nel menu Start
- copia l'interfaccia web che serve al connector per il telefono
- la disinstallazione non cancella chiavi, conversazioni e memoria
- non è firmato: su un PC con Smart App Control o AppLocker aziendale il
  binario può essere bloccato lo stesso, e in quel caso serve un'esenzione
  dall'IT oppure la firma del codice

### Se Windows blocca l'eseguibile

Sintomo: `Un criterio di controllo dell'applicazione ha bloccato il file`.

La procedura di diagnosi e le soluzioni, con i comandi da eseguire e cosa
significa ciascun risultato, sono in [DIAGNOSI_BLOCCO_WINDOWS.md](DIAGNOSI_BLOCCO_WINDOWS.md).

## Installazione con lo zip e installa.ps1 (alternativa)

    Expand-Archive lulix-win-dist-r11.zip -DestinationPath lulix-r11
    cd lulix-r11
    powershell -ExecutionPolicy Bypass -File .\installa.ps1 -Pacchetto ..\lulix-win-dist-r11.zip -Sha256Atteso 8a19ecbab4e646298998c9705cdfbd6d38faec7c02e279a64e3cfdb677947345

Poi, in una finestra nuova:

    lulix setup
    lulix connect setup
    lulix connect pair

`setup` chiede provider e chiave; `connect pair` stampa il codice a sei cifre da
inserire nella web app dal telefono.

## Ultima versione

| Versione | File | Byte | SHA-256 |
|---|---|---|---|
| r13 | lulix-setup-r13.exe | 57.661.987 | 99ee2e1348fb33485db2690610f1921f0d967c857c278792e290a368b995b7a2 |
| r11 | lulix-win-dist-r11.zip | 88.960.057 | 8a19ecbab4e646298998c9705cdfbd6d38faec7c02e279a64e3cfdb677947345 |

## Verifica

Ogni pacchetto porta la sua impronta qui sopra e nel titolo della release.
Prima di installare confrontala:

    Get-FileHash .\lulix-setup.exe -Algorithm SHA256
