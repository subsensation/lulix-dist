# Il setup di Lulix, passo per passo

Dopo l'installazione si lancia una volta:

    lulix setup

Se il comando non viene riconosciuto, la finestra PowerShell è vecchia e non ha
ancora il PATH nuovo: usa il percorso completo, che funziona sempre.

    & "$env:LOCALAPPDATA\Programs\Lulix\lulix.exe" setup

Il setup fa nove passi. Si può interrompere e riprendere: quello che hai già
inserito resta.

## 1 di 9 — Main agent model

Chiede il cervello principale. Scegli:

    1

cioè **DeepSeek V4.1 Flash 1017**, poi incolla la chiave OpenRouter quando la
chiede. La chiave si crea o si copia da https://openrouter.ai/keys e comincia
con `sk-or-v1`. È come denaro: non incollarla in una chat o in un documento.

Se hai più provider puoi cambiarlo dopo con `/provider`.

## 2 di 9 — OpenAI key (research, voice, images, OCR)

Serve per la ricerca web, la voce, la generazione di immagini e il
riconoscimento del testo nelle foto. È facoltativa: se non ce l'hai, premi
Invio e salti. Potrai aggiungerla dopo.

## 3 di 9 — Serper key (web search, optional)

Ricerca web tramite Serper. Facoltativa: Invio per saltare.

## 4 di 9 — Jina key (page reading, optional)

Lettura delle pagine web tramite Jina. Facoltativa: Invio per saltare.

## 5 di 9 — Your name

Il tuo nome, come vuoi che Lulix ti chiami. Scrivilo e premi Invio.

## 6 di 9 — Permissions

Chiede i permessi per restare sveglio durante il lavoro. Accetta.

## 7 di 9 — Lulix's toolchain (optional)

Strumenti aggiuntivi per leggere PDF, immagini e documenti. Facoltativo: puoi
premere Invio e farlo dopo, oppure accettare se vuoi che li installi subito.

## 8 di 9 — Telegram (optional)

Non serve: il canale di Lulix è la web app sul telefono. Premi Invio per
saltare. Se un giorno vuoi Telegram, si configura dopo.

## 9 di 9 — Lulix Connect

Non chiede niente: prepara il canale locale, l'host in background e
l'interfaccia per il telefono. Se dice che manca qualcosa, il messaggio dice
cosa manca.

## Dopo il setup: accoppiare il telefono

    lulix connect setup
    lulix connect pair

Il secondo comando stampa un indirizzo (per esempio `http://192.168.1.20:8733`)
e un codice a sei cifre. Dal telefono, collegato alla stessa rete Wi-Fi del PC,
apri l'indirizzo nel browser e inserisci il codice.

Il codice dura cinque minuti e serve una volta sola: se scade, rilancia
`lulix connect pair`.

## Comandi utili del giorno dopo

    lulix connect status

dice se l'host è vivo, se il canale ascolta e su quale indirizzo.

    lulix setup

si può rilanciare quando vuoi: cambia solo ciò che tocchi.

    lulix --version

la versione installata.

## Se qualcosa non parte

- «lulix non è riconosciuto» → finestra PowerShell vecchia: chiudila e aprine
  una nuova, oppure usa il percorso completo
- «Un criterio di controllo dell'applicazione ha bloccato il file» → vedi
  [DIAGNOSI_BLOCCO_WINDOWS.md](DIAGNOSI_BLOCCO_WINDOWS.md)
- il telefono non si accoppia → telefono e PC sulla stessa rete Wi-Fi, e
  `lulix connect status` per vedere se il canale ascolta
