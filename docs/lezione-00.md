# Lezione 0 — Setup: Installazione e configurazione

**Obiettivo:** Setup completo dell'ambiente di lavoro | **Tempo stimato:** 15 minuti

---

## Indice

1. [Git vs GitHub — chiarimenti fondamentali](#01-git-vs-github--chiarimenti-fondamentali)
2. [Antigravity e la connessione Remote-SSH](#02-antigravity-e-la-connessione-remote-ssh)
3. [Configurazione Git](#03-configurazione-git)
4. [Chiave SSH per GitHub](#04-chiave-ssh-per-github)
5. [GitHub CLI (gh)](#05-github-cli-gh)
6. [Verifica finale](#06-verifica-finale)

---

## 0.1 Git vs GitHub — chiarimenti fondamentali

Due cose diverse che spesso vengono confuse:

| | Git | GitHub |
|---|---|---|
| **Cos'e** | Strumento locale di versionamento | Piattaforma remota per collaborare |
| **Dove vive** | Sul tuo computer | Su internet (server GitHub) |
| **Serve a** | Tenere traccia delle modifiche al codice | Condividere codice e collaborare con altri |
| **Serve internet?** | No | Si |

**L'analogia giusta:** Git è come un salvataggio locale dei videogiochi — lo fai sul tuo computer e non serve internet. GitHub è come il cloud dove condividi quei salvataggi con altri giocatori.

Git nasce nel 2005 da Linus Torvalds (lo stesso creatore di Linux). GitHub nasce nel 2008 come piattaforma per ospitare repository Git. Oggi GitHub appartiene a Microsoft ed è il servizio più usato al mondo per collaborare su codice.

```
Git (locale)           GitHub (remoto)
┌──────────────┐       ┌──────────────┐
│ commit       │ push  │              │
│ branch       │──────►│  repository  │
│ log          │       │  pull request│
│ diff         │◄──────│  issues      │
│ merge        │ pull  │  actions     │
└──────────────┘       └──────────────┘
```

**Regola pratica:** Tutto quello che fai con `git` funziona offline. Tutto quello che coinvolge `github` (push, pull, PR) richiede connessione.

> **Nota GitLab:** Esiste anche **GitLab**, un'alternativa a GitHub. GitLab offre le stesse funzionalità di base (repository, MR, CI/CD) ma è open source e può essere installato on-premise. I concetti di Git (commit, branch, merge) sono identici su entrambe le piattaforme.

---

## 0.2 Antigravity e la connessione Remote-SSH

**Antigravity** è un IDE derivato da VS Code. Simone e Leonardo lo usano per scrivere codice, eseguire comandi e interagire con Git — ma non lavorano sul PC locale. Lavorano su un **server Linux** remoto, collegandosi tramite **Remote-SSH**.

### Come funziona Remote-SSH

Remote-SSH è un meccanismo di VS Code (e quindi anche di Antigravity) che permette di lavorare su un server remoto come se fosse il tuo computer locale:

1. Antigravity apre una connessione SSH verso il server Linux
2. Sul server viene installato automaticamente un piccolo "VS Code Server"
3. Da quel momento, tutto quello che fai in Antigravity gira sul server: terminale, file, estensioni

### Setup della connessione

In Antigravity:

1. `Ctrl+Shift+P` per aprire la Command Palette
2. Cerca **"Remote-SSH: Connect to Host..."**
3. Inserisci le credenziali del server (es. `simone@10.1.1.50`)
4. Inserisci la password quando richiesta
5. Antigravity si riconnette in modalita remota — vedrai l'indicatore "SSH: ..." in basso a sinistra

Una volta connessi:

- Il **terminale integrato** (`View → Terminal` oppure `Ctrl+``) è un **bash sul server Linux**
- Il **File Explorer** mostra il filesystem del server (es. `/home/simone/`)
- Il **Pannello Source Control** (`Ctrl+Shift+G`) funziona sul repository remoto

### Importante

Antigravity **NON è un sostituto di Git** — è un'interfaccia che rende Git più accessibile. Sotto il cofano usa gli stessi comandi `git` che impareremo nel corso.

### Da terminale vs da Antigravity UI

Durante il corso impareremo sia i comandi Git da terminale sia come fare le stesse operazioni tramite l'interfaccia di Antigravity.

| Cosa vuoi fare | Da terminale (bash) | Da Antigravity UI |
|---|---|---|
| Vedere cosa e cambiato | `git status` | Pannello Source Control (sidebar) |
| Aggiungere file allo staging | `git add <file>` | Cliccare `+` accanto al file nel Source Control |
| Fare commit | `git commit -m "messaggio"` | Scrivere messaggio nella barra del Source Control e cliccare ✓ |
| Creare un branch | `git branch nome-branch` | Cliccare il nome branch (in basso a sinistra) → "Create new branch" |
| Vedere le modifiche | `git diff` | Cliccare su un file modificato nel Source Control |

---

## 0.3 Configurazione Git

Git è già preinstallato sul server Linux. Ma deve sapere chi sei. Ogni commit porta il tuo nome e la tua email.

```bash
# Simone
git config --global user.name "Simone Rossi"
git config --global user.email "simone.rossi@acme.com"

# Leonardo
git config --global user.name "Leonardo Bianchi"
git config --global user.email "leonardo.bianchi@acme.com"
```

**Importante:** Usa la stessa email collegata al tuo account GitHub.

**Nota:** Simone e Leonardo hanno ciascuno il proprio account Linux sul server (`/home/simone/` e `/home/leonardo/`). Ognuno esegue questi comandi **nel proprio terminale** — `git config --global` scrive in `~/.gitconfig`, quindi ogni utente Linux ha la propria identità Git. Non c'è conflitto tra le due configurazioni.

### Verifica

```bash
git config --list
```

Cerca queste righe nell'output:

```
user.name=Simone Rossi
user.email=simone.rossi@acme.com
```

### Altre configurazioni utili

```bash
git config --global core.editor "nano"
```

Git a volte apre un editor di testo: per esempio quando fai `git commit` senza messaggio (`-m`), oppure durante un merge o un rebase. Di default usa `vi`, che non e intuitivo se non lo conosci. Con `nano` hai un editor semplice — in basso vedi i comandi (`Ctrl+O` per salvare, `Ctrl+X` per uscire).

Se preferisci usare Antigravity come editor per i commit:

```bash
git config --global core.editor "antigravity --wait"
```

Il flag `--wait` è fondamentale: dice al terminale di aspettare che tu chiuda il file in Antigravity prima di proseguire. Senza `--wait`, il commit partirebbe con un file vuoto e verrebbe annullato.

```bash
git config --global color.ui auto
```

Aggiunge colore all'output di comandi come `git status`, `git log`, `git diff`. Per esempio i file modificati appaiono in rosso, quelli in staging in verde. Utile per leggere più velocemente lo stato del repository. Su terminali moderni è quasi sempre già attivo di default, ma non costa nulla assicurarsene.

```bash
git config --global init.defaultBranch main
```

Quando crei un nuovo repository con `git init`, Git crea il primo branch. Storicamente si chiamava `master`; dal 2020 il nome raccomandato e `main`. Questo comando assicura che ogni nuovo repository usi `main` come branch iniziale, evitando confusione con repository più vecchi che usano ancora `master`.

---

## 0.4 Chiave SSH per GitHub

La chiave SSH ti permette di comunicare con GitHub **senza digitare la password ogni volta**. E il metodo di autenticazione raccomandato.

**Attenzione:** Simone e Leonardo lavorano su un server Linux tramite Remote-SSH. La chiave SSH va generata **sul server Linux**, non sul PC locale. Sarà questa chiave a autenticarsi con GitHub quando fate push o pull dal server.

### Generare la chiave

```bash
# Simone
ssh-keygen -t ed25519 -C "simone.rossi@acme.com"

# Leonardo
ssh-keygen -t ed25519 -C "leonardo.bianchi@acme.com"
```

Quando chiede dove salvare, premi **Invio** per accettare il default. La chiave viene salvata nella home dell'utente corrente: Simone troverà `/home/simone/.ssh/id_ed25519`, Leonardo troverà `/home/leonardo/.ssh/id_ed25519`. Le due chiavi sono completamente indipendenti — ogni utente ha la propria cartella `.ssh/`.

Quando chiede la passphrase:
- **Per il corso:** premi Invio due volte (nessuna passphrase, più comodo)
- **Per la produzione:** inserisci una passphrase sicura

### Copiare la chiave pubblica

```bash
cat ~/.ssh/id_ed25519.pub
```

L'output sarà qualcosa del genere:

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... simone.rossi@acme.com
```

**Copia tutto l'output** (da `ssh-ed25519` fino all'email).

### Aggiungere la chiave su GitHub

1. Vai su **github.com** e fai login
2. Clicca la tua foto profilo (in alto a destra) → **Settings**
3. Nel menu laterale: **SSH and GPG keys**
4. Clicca **New SSH key**
5. **Title:** qualcosa come "Server Linux di Simone" (descrittivo, per te)
6. **Key type:** Authentication Key
7. **Key:** incolla la chiave pubblica copiata prima
8. Clicca **Add SSH key**

### Testare la connessione

```bash
ssh -T git@github.com
```

Se tutto funziona, vedrai:

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

Se è la prima volta che ti connetti, Git chiedera di confermare il fingerprint. Rispondi `yes`.

> **Nota GitLab:** Su GitLab il procedimento è identico: Settings → SSH Keys → Add key. L'URL del remote sarà `git@gitlab.com:username/repo.git` invece di `git@github.com:username/repo.git`.

---

## 0.5 GitHub CLI (`gh`)

La GitHub CLI è lo strumento da riga di comando ufficiale di GitHub. Permette di gestire PR, issue e repository direttamente dal terminale.

### Installazione su Linux

**Opzione A — Ubuntu/Debian:**

```bash
sudo apt install gh
```

**Opzione B — Fedora:**

```bash
sudo dnf install gh
```

**Opzione C — Download manuale:**

Scarica il pacchetto da https://github.com/cli/cli/releases/latest e installalo.

> **Nota:** Se non hai i permessi sudo, chiedi all'amministratore del server.

> **Nota:** Se non riesci a installare gh, non preoccuparti: nel corso mostreremo sempre l'alternativa via interfaccia web di GitHub.

### Autenticazione

```bash
gh auth login
```

Segui la procedura guidata:

```
? What is your preferred protocol for Git operations?  SSH
? How would you like to authenticate GitHub CLI?  Login with a web browser
! First copy your one-time code: XXXX-XXXX
Press Enter to open github.com in your browser...
```

1. Copia il codice one-time
2. Premi Invio — si apre il browser (sul tuo PC locale, non sul server)
3. Incolla il codice nel browser e autorizza

**Se il browser non si apre dal server:** copia il codice one-time, poi apri manualmente https://github.com/login/device sul tuo PC e incollalo.

### Dove vengono salvate le credenziali

Dopo il login, `gh` salva un **token OAuth** in un file locale: `~/.config/gh/hosts.yml`. Questo file vive nella home dell'utente corrente — quindi Simone avra il suo token in `/home/simone/.config/gh/hosts.yml` e Leonardo in `/home/leonardo/.config/gh/hosts.yml`. Da questo momento in poi, ogni comando `gh` (creare PR, leggere issue, ecc.) usa automaticamente quel token. Non serve rifare il login a ogni sessione, a meno che il token non scada o venga revocato da GitHub.

### Verifica

```bash
gh auth status
```

Output atteso:

```
github.com
  ✓ Logged in to github.com as simone-rossi (oauth_token)
  ✓ Git operations for github.com configured to use ssh protocol.
  ✓ Token: gho_************************************
     ✓ Token scopes: gist, read:org, repo
```

> **Nota GitLab:** GitLab ha il suo CLI chiamato `glab`. Si installa con `sudo apt install glab` (Linux) o `brew install glab`. Il comando `glab auth login` sostituisce `gh auth login`. I comandi sono simili: `glab mr create` invece di `gh pr create`, `glab mr view` invece di `gh pr view`.

---

## 0.6 Verifica finale

Esegui questo checklist. Ogni punto deve risultare verde prima di passare alla prossima lezione.

```bash
# 1. Git configurato
git config user.name
git config user.email

# 2. SSH attiva
ssh -T git@github.com

# 3. GitHub CLI autenticato
gh auth status
```

### Checklist rapido

| # | Verifica | Comando | Risultato atteso |
|---|---|---|---|
| 1 | Git configurato | `git config user.name` | Il tuo nome |
| 2 | Email corretta | `git config user.email` | La tua email GitHub |
| 3 | SSH funzionante | `ssh -T git@github.com` | "Hi username! ..." |
| 4 | gh autenticato | `gh auth status` | "Logged in as ..." |

**Se qualcosa non funziona:** torna alla sezione corrispondente e ripeti. Non proseguire senza aver verificato tutto.

---

## Riepilogo comandi

```
# === Configurazione Git ===
git config --global user.name "Simone Rossi"
git config --global user.email "simone.rossi@acme.com"
git config --global core.editor "nano"
git config --global color.ui auto
git config --global init.defaultBranch main
git config --list

# === Chiave SSH ===
ssh-keygen -t ed25519 -C "simone.rossi@acme.com"
cat ~/.ssh/id_ed25519.pub
ssh -T git@github.com

# === GitHub CLI ===
sudo apt install gh
gh auth login
gh auth status
```

---

*Prossima lezione: [Lezione 1 — Repository: init, clone e struttura di un progetto Git]*
