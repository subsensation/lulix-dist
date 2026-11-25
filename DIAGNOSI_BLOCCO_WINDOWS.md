# Quando Windows blocca Lulix

Windows può rifiutarsi di eseguire `lulix.exe` e rispondere:

    Impossibile eseguire il programma 'lulix.exe': Un criterio di controllo
    dell'applicazione ha bloccato il file

Non è un difetto dell'installazione e non è un virus: è una policy di Windows
che accetta solo programmi firmati o approvati. Questo file spiega come capire
quale policy è e cosa fare, in ordine di rapidità.

## 1. Capire quale policy è

Apri PowerShell e incolla uno alla volta questi tre comandi.

    Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\CI\Policy" -Name VerifiedAndReputablePolicyState -ErrorAction SilentlyContinue

    (Get-CimInstance -Namespace root/Microsoft/Windows/DeviceGuard -ClassName Win32_DeviceGuard).CodeIntegrityPolicyEnforcementStatus

    Get-WinEvent -LogName "Microsoft-Windows-AppLocker/EXE and DLL" -MaxEvents 5 -ErrorAction SilentlyContinue | Select-Object TimeCreated, Id, Message

### Come leggerli

**Primo comando, `VerifiedAndReputablePolicyState`**

- `1` → è attivo **Smart App Control**: accetta solo programmi con reputazione
  nota o firmati. È la causa più comune sui PC nuovi.
- `0`, oppure il comando non risponde → Smart App Control non è attivo, guarda
  gli altri due.

**Secondo comando, `CodeIntegrityPolicyEnforcementStatus`**

- `2` → è attiva una policy **WDAC** (Windows Defender Application Control),
  tipica dei PC aziendali gestiti. Non si aggira: serve un'eccezione da chi
  amministra la flotta.
- `0` o `1` → WDAC non sta bloccando.

**Terzo comando, eventi AppLocker**

Se compare un evento con il percorso di `lulix.exe`, la policy è **AppLocker**
e la regola che blocca è scritta nel messaggio dell'evento.

## 2. Cosa disattivare, in ordine

Apri **Sicurezza di Windows**: menu Start, scrivi «Sicurezza di Windows», invio.
Oppure l'icona a scudo nella barra delle applicazioni.

### 2.1 SmartScreen — è quello che chiede conferma

Percorso: **Controllo app e browser → Impostazioni di Protezione basata su
reputazione → Controlla app e file**.

Metti su **Disattiva**. Effetto: Windows non chiede più conferma prima di
eseguire un programma non firmato. Non tocca l'antivirus e non abbassa la
protezione dei file: riguarda solo l'avviso «Windows ha protetto il PC».

Se dopo questo il setup parte, hai finito: puoi riaccenderlo quando vuoi.

### 2.2 Smart App Control — è quello che blocca senza chiedere

Si riconosce da questo messaggio, con il pulsante **Ok** e nient'altro:

    Controllo intelligente delle app ha bloccato un'app che potrebbe non
    essere sicura
    Abbiamo bloccato C:\Users\<nome>\Downloads\lulix-setup.exe perché non è
    stato possibile verificare il rispettivo server di pubblicazione

«Controllo intelligente delle app» è il nome italiano di Smart App Control.
Non c'è nessun «Esegui comunque»: o si disattiva, o non parte.

Percorso: **Sicurezza di Windows → Controllo app e browser → Impostazioni
Smart App Control** (in italiano **Controllo intelligente delle app**) →
**Disattiva**.

Attenzione, e non è una formalità: una volta spento non si riaccende senza
reinstallare Windows. Se il PC non è tuo, questa decisione non è tua.

Se la voce è grigia o non c'è, la disattivazione è imposta da una policy
aziendale: vale il punto 2.4.

### 2.3 Antivirus, solo se il messaggio parla di minaccia

Se dice «minaccia trovata» o «file in quarantena», non è un criterio di
controllo: è Defender. Percorso: **Protezione dalle minacce e dai virus →
Impostazioni di Protezione dalle minacce → Esclusioni → Aggiungi esclusione →
Cartella**, e indica

    %LOCALAPPDATA%\Programs\Lulix

Poi rilancia l'installer. È una scelta consapevole: stai dichiarando di fidarti
di quella cartella.

### 2.4 Policy aziendale — non si disattiva

Se nei comandi del punto 1 è risultato WDAC `2`, oppure negli eventi AppLocker
compare il percorso di Lulix, il blocco viene da una policy scritta
dall'amministratore della flotta. Non c'è nessuna spunta da togliere: serve
un'eccezione, e la strada è la B qui sotto.

## 3. Le soluzioni quando la disattivazione non è possibile

### A. Smart App Control attivo (primo comando = 1)

Si spegne da **Sicurezza di Windows → Controllo app e browser → Impostazioni
Smart App Control → Disattiva**. Attenzione: una volta spento non si riaccende
senza reinstallare Windows. È una scelta del proprietario del PC, non
dell'utente.

### B. Policy aziendale AppLocker o WDAC

Serve un'eccezione dall'IT. Per chiederla, comunica queste tre cose:

- percorso del programma: `%LOCALAPPDATA%\Programs\Lulix\lulix.exe`
- impronta del file:

      Get-FileHash "$env:LOCALAPPDATA\Programs\Lulix\lulix.exe" -Algorithm SHA256

- la versione: `0.1.0-r11`

Con l'impronta, l'IT può creare una regola hash senza firmare nulla.

### C. Provare un'altra cartella

Se il blocco è per percorso e non per firmatario, installare altrove funziona.
Rilancia l'installer e, nella finestra che chiede la cartella, scegli per
esempio:

    C:\Users\<nomeutente>\Lulix

Poi, per usarlo, il percorso completo è:

    & "$env:USERPROFILE\Lulix\lulix.exe" setup

### D. Firma del codice (soluzione definitiva)

Con un certificato di firma del codice il programma diventa attendibile e il
blocco sparisce su qualunque PC, senza intervento dell'IT. È una spesa annuale
e richiede la verifica dell'identità del titolare. Finché non c'è, restano le
strade A, B e C.

## 4. Test rapido: è il percorso o il file?

Se hai ancora il pacchetto estratto (per esempio `C:\Users\lucky\Desktop\lulix-r11`),
esegui da lì:

    & "$env:USERPROFILE\Desktop\lulix-r11\lulix.exe" --version

- se **parte**, il blocco riguarda la cartella AppData: usa la soluzione C
- se **non parte**, il blocco riguarda il programma in sé: servono A, B o D

## 5. Cosa NON è

- non è il marchio «scaricato da internet»: quello si toglie con `Unblock-File`
  ed è già stato fatto dall'installer
- non è mancanza di privilegi di amministratore: Lulix si installa per-utente
- non è un antivirus che ha messo in quarantena il file: in quel caso il
  messaggio parlerebbe di minaccia, non di criterio di controllo
