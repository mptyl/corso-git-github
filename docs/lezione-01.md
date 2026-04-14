# Lezione 1 — Simone crea il progetto RistoranteAPI

**Obiettivo:** Creare il repository, impostare la struttura del progetto e la branch strategy

**Tempo stimato:** 15 minuti

---

## 1.1 La storia finora

Simone ha un'idea: un sistema per gestire il menu di un ristorante. Vuole chiamarlo **RistoranteAPI** e collaborare con Leonardo, un amico sviluppatore.

Prima di poter scrivere una sola riga di codice, serve una base solida: un repository Git su GitHub dove entrambi possano lavorare.

---

## 1.2 Creazione del repository

Simone apre il terminale di Antigravity (`Ctrl+`` `) e crea il progetto:

```bash
mkdir -p ~/progetti/RistoranteAPI
cd ~/progetti/RistoranteAPI
```

Poi inizializza il repository Git locale:

```bash
git init
```

`git init` crea una cartella nascosta `.git/` che contiene tutto lo stato del repository: cronologia dei commit, branch, configurazione. Senza questa cartella, non c'e Git.

Ora Simone vuole pubblicare il progetto su GitHub per condividerlo con Leonardo:

```bash
gh repo create RistoranteAPI --public --source=. --push
```

Cosa fa ogni flag:

| Flag | Significato |
|---|---|
| `--public` | Il repository sara visibile a tutti |
| `--source=.` | Usa la cartella corrente come sorgente |
| `--push` | Invia subito il contenuto locale al remote |

Il comando `gh` e la **GitHub CLI**, uno strumento da terminale per interagire con GitHub senza aprire il browser.

> **Se `gh` non e installato:** Crea il repository dal browser:
>
> 1. Vai su [github.com](https://github.com) → clicca **New repository**
> 2. Nome: `RistoranteAPI` → **Public** → **Create repository**
> 3. Poi collega il remote dal terminale:
>
> ```bash
> git remote add origin git@github.com:SimoneRossi/RistoranteAPI.git
> ```

> **Nota GitLab:** Su GitLab, il repository si crea dall'interfaccia web (gitlab.com → New Project) oppure con `glab repo create RistoranteAPI --public`. Non esiste un equivalente esatto di `gh repo create --source=. --push`: dopo aver creato il repo su GitLab, devi aggiungere il remote manualmente con `git remote add origin git@gitlab.com:SimoneRossi/RistoranteAPI.git` e poi `git push -u origin main`.

---

## 1.3 Struttura del progetto

Simone crea la struttura delle cartelle e dei file:

```
~/progetti/RistoranteAPI/
├── README.md
├── src/
│   ├── menu.md
│   ├── piatti.md
│   └── ricette.md
```

In Antigravity: crea la cartella `src` (tasto destro nel Explorer → New Folder).

Il file `README.md` e la porta d'ingresso del progetto: chiunque visiti il repository su GitHub lo vede per primo.

In Antigravity: crea il file `README.md` (tasto destro nel Explorer → New File) e inserisci il seguente contenuto:

```markdown
# RistoranteAPI

Sistema di gestione menu per il Ristorante Da Luigi.
```

Poi il menu del giorno. In Antigravity: crea il file `src/menu.md` (tasto destro nel Explorer → New File) e inserisci il seguente contenuto:

```markdown
# Menu del Giorno

* Pizza Margherita
* Pasta Carbonara
* Tiramisù
```

Il catalogo completo dei piatti. In Antigravity: crea il file `src/piatti.md` (tasto destro nel Explorer → New File) e inserisci il seguente contenuto:

```markdown
# Catalogo Piatti

## Antipasti
* Bruschetta
* Caprese

## Primi
* Carbonara
* Amatriciana

## Secondi
* Cotoletta
* Pesce Spada

## Dolci
* Tiramisù
* Panna Cotta
```

E le ricette. In Antigravity: crea il file `src/ricette.md` (tasto destro nel Explorer → New File) e inserisci il seguente contenuto:

```markdown
# Ricette

## Pizza Margherita
Ingredienti: farina, pomodoro, mozzarella, basilico

## Pasta Carbonara
Ingredienti: spaghetti, guanciale, uova, pecorino
```

---

## 1.4 Il primo commit

Ora Simone salva tutto nel primo commit.

```bash
git add .
```

`git add .` aggiunge tutti i file nuovi e modificati alla **staging area** — uno spazio intermedio dove prepari i cambiamenti prima di registrarli definitivamente.

```bash
git commit -m "Init: struttura progetto RistoranteAPI"
```

`git commit` registra uno snapshot permanente dei file nella staging area. Il flag `-m` permette di scrivere il messaggio di commit direttamente da riga di comando. Il messaggio segue la convenzione `tipo: descrizione` — in questo caso `Init` indica l'inizializzazione del progetto.

```bash
git push origin main
```

`git push` invia i commit locali al repository remoto su GitHub. `origin` e il nome predefinito del remote, `main` e il branch corrente.

> **Alternativa con Antigravity:** Apri il pannello **Source Control** (`Ctrl+Shift+G`). Vedrai tutti i file modificati. Clicca `+` accanto a ogni file per stagearli, scrivi il messaggio di commit nella casella di testo e clicca `✓` per committare. Poi clicca **Sync Changes** per il push.

---

## 1.5 La branch strategy: main + development

Simone vuole che il progetto abbia due branch principali:

- **`main`** — codice production-ready, sempre stabile
- **`development`** — branch di lavoro dove lui e Leonardo sperimentano

Perche due branch separati? Perche lavorare direttamente su `main` e rischioso: un errore puo rompere il codice funzionante. Con `development` come branch di lavoro, `main` rimane sempre pulito.

Simone crea il branch di sviluppo:

```bash
git checkout -b development
```

Il flag `-b` crea il branch nuovo e ci sposta sopra contemporaneamente. Equivalente a `git branch development && git checkout development`.

> **Alternativa con Antigravity:** Clicca sul nome del branch in basso a sinistra nella status bar → **Create new branch** → digita `development` e premi Invio.

Pubblica il branch su GitHub:

```bash
git push -u origin development
```

Il flag `-u` (alias di `--set-upstream`) dice a Git di tracciare questo branch locale con il branch remoto `origin/development`. Dal prossimo push, bastera `git push` senza specificare remote e branch.

### Impostare development come branch predefinito

Su GitHub, Simone cambia il branch predefinito:

1. Va su **Settings → Branches**
2. In **Default branch**, cambia da `main` a `development`
3. Conferma con **Update**

Perche e utile? Quando qualcuno clona il repository, Git posiziona l'utente sul branch predefinito. Se il default e `development`, chi clona si trova subito sul branch di lavoro, senza rischio di modificare `main` per sbaglio. Inoltre, le Pull Request su GitHub vengono aperte automaticamente verso il branch predefinito.

> **Nota GitLab:** Su GitLab il branch predefinito si imposta in Settings → Repository → Default branch. Il procedimento e' identico. GitLab chiama "Merge Requests" quello che GitHub chiama "Pull Requests" — vedremo le differenze nella Lezione 5.

---

## 1.6 Cosa e successo — dietro le quinte

Dietro la semplicita di questi comandi c'e molto che accade.

### Remote tracking branches

Quando Simone ha fatto `git push -u origin development`, Git ha creato una connessione tra il branch locale `development` e il branch remoto `origin/development`. Questo permette a comandi come `git pull` e `git status` di sapere se il branch locale e avanti o indietro rispetto al remoto.

Per vedere tutti i branch — locali e remoti:

```bash
git branch -a
```

L'output sara qualcosa del genere:

```
* development
  main
  remotes/origin/main
  remotes/origin/development
```

I branch con prefisso `remotes/` sono copie locali dei branch remoti, aggiornate al momento dell'ultimo `git fetch` o `git push`.

Per vedere i remote configurati:

```bash
git remote -v
```

L'output mostra gli URL associati al nome `origin`:

```
origin  git@github.com:SimoneRossi/RistoranteAPI.git (fetch)
origin  git@github.com:SimoneRossi/RistoranteAPI.git (push)
```

`(fetch)` e l'URL usato per scaricare dati (`git pull`, `git fetch`), `(push)` e l'URL usato per inviarli (`git push`).

---

## Riepilogo comandi

| Comando | Cosa fa |
|---|---|
| `mkdir -p ~/progetti/RistoranteAPI` | Crea la cartella del progetto |
| `cd ~/progetti/RistoranteAPI` | Entra nella cartella del progetto |
| `git init` | Inizializza il repository Git locale |
| `gh repo create RistoranteAPI --public --source=. --push` | Crea il repo su GitHub e fa il primo push |
| Creazione cartella `src` in Antigravity | Crea la cartella sorgente |
| Creazione file in Antigravity | Crea i file con contenuto |
| `git add .` | Aggiunge tutti i file alla staging area |
| `git commit -m "messaggio"` | Registra uno snapshot dei cambiamenti |
| `git push origin main` | Invia i commit al branch remoto |
| `git checkout -b development` | Crea e passa al branch development |
| `git push -u origin development` | Pubblica il branch e imposta il tracciamento |
| `git branch -a` | Mostra tutti i branch (locali e remoti) |
| `git remote -v` | Mostra gli URL dei remote configurati |

---

*Prossima lezione: Leonardo clona il repository e fa la sua prima modifica.*
