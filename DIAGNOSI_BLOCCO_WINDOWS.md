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

## 2. Le soluzioni, dalla più rapida

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

## 3. Test rapido: è il percorso o il file?

Se hai ancora il pacchetto estratto (per esempio `C:\Users\lucky\Desktop\lulix-r11`),
esegui da lì:

    & "$env:USERPROFILE\Desktop\lulix-r11\lulix.exe" --version

- se **parte**, il blocco riguarda la cartella AppData: usa la soluzione C
- se **non parte**, il blocco riguarda il programma in sé: servono A, B o D

## 4. Cosa NON è

- non è il marchio «scaricato da internet»: quello si toglie con `Unblock-File`
  ed è già stato fatto dall'installer
- non è mancanza di privilegi di amministratore: Lulix si installa per-utente
- non è un antivirus che ha messo in quarantena il file: in quel caso il
  messaggio parlerebbe di minaccia, non di criterio di controllo
