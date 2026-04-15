# Lezione 9 — Documentazione e GitHub Pages

**Obiettivo:** Creare documentazione con MkDocs e pubblicarla su GitHub Pages

**Tempo stimato:** 25 minuti

---

## 9.1 Perché la documentazione?

Un progetto senza documentazione è un **progetto morto**. Chiunque arrivi dopo — un nuovo sviluppatore, un collaboratore, o te stesso tra sei mesi — deve poter capire cosa fa il progetto, come è strutturato e come contribuire.

La documentazione non è un extra: fa parte del codice. Se non è scritta, è come se non esistesse.

Per pubblicarla usiamo due strumenti:

- **GitHub Pages** — hosting gratuito per siti statici, integrato nel repository GitHub
- **MkDocs** — generatore di siti statici da file Markdown, perfetto per documentazione tecnica

Il piano:

1. Creare una cartella `docs/` con la documentazione in Markdown
2. Configurare `mkdocs.yml` con tema, navigazione ed estensioni
3. Abilitare GitHub Pages con un workflow GitHub Actions che pubblica automaticamente il sito

---

## 9.2 Simone crea il branch per la documentazione

La documentazione segue lo stesso flusso del codice: branch feature, PR, code review.

```bash
# Simone
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development
git worktree add ../RistoranteAPI-docs feature/06-documentazione
cd ../RistoranteAPI-docs
```

Simone crea un worktree isolato per lavorare sulla documentazione senza interferire con altro lavoro in corso.

---

## 9.3 Struttura della documentazione

Simone crea la cartella e i file Markdown che compongono il sito.

In Antigravity: crea la cartella `docs` (tasto destro nel Explorer → New Folder).

Per ogni file: tasto destro sulla cartella `docs` → **New File** → inserisci il nome. Copia il contenuto seguente nel file e salva con **Ctrl+S**.

### docs/index.md

La home page del sito — la prima cosa che un visitatore vede:

```markdown
# RistoranteAPI

Sistema di gestione menu per il **Ristorante Da Luigi**.

## Panoramica

RistoranteAPI e' un sistema per la gestione digitale del menu del ristorante,
con supporto per catalogo piatti, ricette, e informazioni sugli allergeni.

## Sezioni

* [Catalogo Piatti](piatti.md)
* [Menu del Giorno](menu.md)
* [Ricette](ricette.md)
* [Guida alla Contribuzione](contribuire.md)
```

### docs/piatti.md

Il catalogo dei piatti. Crea il file `docs/piatti.md` e incolla:

```markdown
# Catalogo Piatti

Il catalogo completo dei piatti del Ristorante Da Luigi.

## Categorie

Il menu e' organizzato in categorie:

* **Antipasti** — per iniziare con gusto
* **Primi** — la tradizione italiana
* **Secondi** — piatti principali
* **Dolci** — per chiudere in bellezza
* **Piatti Speciali** — creazioni dello chef

> Tutti i piatti indicano gli allergeni presenti.
```

### docs/menu.md

Le istruzioni per aggiornare il menu. Crea il file `docs/menu.md` e incolla:

```markdown
# Menu del Giorno

Il menu viene aggiornato giornalmente dal team.

## Come si aggiorna

1. Creare un branch feature da `development`
2. Modificare `src/menu.md`
3. Creare una Pull Request verso `development`
4. Ottenere approvazione dal team
5. Mergiare la PR

## Convenzioni

* Ogni piatto deve indicare gli allergeni tra parentesi
* I prezzi sono IVA inclusa
* Le modifiche al menu sono soggette a code review
```

### docs/ricette.md

Le ricette dei piatti. Crea il file `docs/ricette.md` e incolla:

```markdown
# Ricette

Le ricette dei piatti del Ristorante Da Luigi.

## Formato ricetta

Ogni ricetta segue questo formato:

```
## Nome del Piatto
Ingredienti: elenco ingredienti separati da virgola
Preparazione: descrizione della preparazione
```

## Ricette disponibili

* Pizza Margherita
* Pasta Carbonara
* Ravioli di Ricotta e Spinaci
* Bruschetta al Pomodoro
* Tiramisu'
* Panna Cotta
```

### docs/contribuire.md

La guida per chi vuole contribuire al progetto. Crea il file `docs/contribuire.md` e incolla:

```markdown
# Guida alla Contribuzione

Grazie per il tuo interesse nel contribuire a RistoranteAPI!

## Processo di sviluppo

1. Crea un branch feature da `development`
   ```bash
   git checkout development
   git pull origin development
   git checkout -b feature/tua-feature
   ```

2. Fai le tue modifiche e committa
   ```bash
   git add .
   git commit -m "feat: descrizione della modifica"
   ```

3. Pusha e crea una Pull Request
   ```bash
   git push -u origin feature/tua-feature
   ```

4. Vai su GitHub e crea una Pull Request verso `development`

5. Attendi la code review e l'approvazione

## Convenzioni dei commit

* `feat:` — nuova funzionalita'
* `fix:` — correzione bug
* `docs:` — modifica documentazione
* `hotfix:` — fix urgente in produzione
* `merge:` — risoluzione conflitto

## Branch naming

* `feature/NN-descrizione` — nuove funzionalita'
* `hotfix/descrizione` — fix urgenti
* `docs/descrizione` — aggiornamenti documentazione
```

Ogni pagina ha uno scopo preciso. La home da' il contesto, le pagine interne spiegano i dettagli, la guida alla contribuzione rende il progetto accessibile a nuovi sviluppatori.

---

## 9.4 Il file mkdocs.yml

Il cuore della configurazione MkDocs. Simone crea il file nella root del progetto (non dentro `docs/`).

In Antigravity: tasto destro nella root del progetto (la cartella `RistoranteAPI-docs`) → **New File** → `mkdocs.yml`. Copia il contenuto seguente e salva con **Ctrl+S**.

```yaml
site_name: RistoranteAPI
site_description: Sistema di gestione menu per il Ristorante Da Luigi
site_author: Simone Rossi & Leonardo Bianchi

theme:
  name: material
  language: it
  palette:
    primary: indigo
    accent: indigo
  features:
    - navigation.tabs
    - navigation.sections
    - navigation.top

nav:
  - Home: index.md
  - Piatti: piatti.md
  - Menu: menu.md
  - Ricette: ricette.md
  - Contribuire: contribuire.md

markdown_extensions:
  - admonition
  - codehilite
  - toc:
      permalink: true
```

Ogni sezione ha un ruolo:

| Sezione | Cosa fa |
|---|---|
| `site_name` | Il nome del sito — appare nell'intestazione è nel titolo della pagina |
| `site_description` | La descrizione — usata nei metadati HTML |
| `site_author` | Gli autori — appare nel footer |
| `theme.name: material` | Usa **MkDocs Material** — il tema più diffuso per documentazione tecnica |
| `theme.language: it` | Interfaccia del tema in italiano |
| `theme.palette` | Colori primario è di accento |
| `theme.features` | Navigazione a tab, sezioni, pulsante "torna su" |
| `nav` | La struttura del menu laterale — definisce l'ordine delle pagine |
| `markdown_extensions` | Estensioni: admonition (box evidenziati), codehilite (syntax highlighting), toc (indice con link permanenti) |

La sezione `nav` è quella che mappa i file Markdown alle voci del menu. Se non la specifichi, MkDocs rileva automaticamente i file, ma definirla esplicitamente ti da' pieno controllo sull'ordine è sulla gerarchia.

---

## 9.5 Commit e PR

Simone committa tutto e crea la PR verso `development`.

**Da terminale:**

```bash
# Simone
git add docs/ mkdocs.yml
git commit -m "docs: aggiunge documentazione MkDocs e configurazione GitHub Pages"
git push -u origin feature/06-documentazione
```

Per creare la PR, vai su GitHub nel browser e clicca **"Compare & pull request"**, oppure usa `gh pr create` se hai GitHub CLI installato.

**In alternativa con Antigravity Source Control:**

1. Apri il pannello **Source Control** (icona con i rami sulla barra laterale sinistra, o **Ctrl+Shift+G**)
2. Vedrai tutti i file modificati e nuovi elencati sotto "Changes"
3. Clicca l'icona **+** accanto a ogni file per stagearli (equivalente di `git add`)
4. Inserisci il messaggio di commit nel campo di testo in alto: `docs: aggiunge documentazione MkDocs e configurazione GitHub Pages`
5. Clicca **Commit** (o premi **Ctrl+Enter**)
6. Poi clicca **Sync Changes** (o l'icona di push) per pushare sul remoto
7. Per la PR, vai su GitHub nel browser e crea la Pull Request manualmente

Il messaggio di commit inizia con `docs:` — la convenzione per le modifiche alla documentazione.

Leonardo revisiona:

```bash
# Leonardo
gh pr review 6 --approve --body "Documentazione completa e ben strutturata. Approvo."
gh pr merge 6 --merge
```

La PR #6 viene approvata e mergiata. La documentazione è ora su `development`.

---

## 9.6 Abilitare GitHub Pages con GitHub Actions

La documentazione è nel repository, ma non è ancora pubblica. Simone abilita GitHub Pages.

### Configurazione su GitHub

1. Andare su **Settings** → **Pages**
2. In **Source**, selezionare **GitHub Actions** (non "Deploy from a branch")

Questo dice a GitHub: "Non cercare un branch con i file HTML. Usa un workflow per costruire e pubblicare il sito."

### Il workflow GitHub Actions

Simone crea il file `.github/workflows/pages.yml`.

In Antigravity: crea la struttura di cartelle `.github/workflows/` (New Folder due volte) è il file `pages.yml` (New File). Inserisci:

> Nota: `.github` inizia con un punto. Su Linux le cartelle con punto iniziale sono native — nessun problema.

```yaml
name: Deploy MkDocs to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install mkdocs-material
      - run: mkdocs build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: site

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/deploy-pages@v4
         id: deployment
```

> **Nota GitLab:** GitHub Actions e GitLab CI/CD sono concettualmente simili ma usano file diversi:
> - GitHub: `.github/workflows/pages.yml` con sintassi specifica
> - GitLab: `.gitlab-ci.yml` nella root del progetto con sintassi GitLab
>
> Su GitLab, il job per le Pages deve chiamarsi obbligatoriamente `pages` e produrre un artefatto nella directory `public/` (non `site/`):
> ```yaml
> pages:
>   stage: deploy
>   script:
>     - pip install mkdocs-material
>     - mkdocs build -d public
>   artifacts:
>     paths:
>       - public
>   only:
>     - main
> ```
> GitLab Pages è gratuito su gitlab.com ma richiede che il progetto sia visibile (public o, su self-hosted, accessibile). L'URL sarà `https://username.gitlab.io/reponame/`.

### Cosa fa ogni parte

**GitHub Actions** è un sistema CI/CD integrato in GitHub. Esegue automaticamente dei workflow — sequenze di comandi — in risposta a eventi come push, PR, o trigger manuali.

Il workflow si attiva:

| Trigger | Quando |
|---|---|
| `push` su `main` | Ogni volta che il branch principale riceve nuovi commit |
| `workflow_dispatch` | Manualmente, dal tab Actions su GitHub |

I due job funzionano in sequenza:

**Job `build`** — costruisce il sito:

1. `actions/checkout@v4` — scarica il codice dal repository
2. `actions/setup-python@v5` — installa Python 3.12
3. `pip install mkdocs-material` — installa MkDocs con il tema Material
4. `mkdocs build` — trasforma i file Markdown in un sito HTML statico dentro `site/`
5. `actions/upload-pages-artifact@v3` — carica la cartella `site/` come artefatto

**Job `deploy`** — pubblica il sito:

1. Prende l'artefatto dal job `build`
2. Lo pubblica su GitHub Pages usando `actions/deploy-pages@v4`
3. Genera l'URL del sito

La sezione `concurrency` assicura che un solo deploy giri alla volta — se un nuovo push arriva mentre un deploy è in corso, il secondo aspetta.

---

## 9.7 Mergiare su main per pubblicare

La documentazione è su `development`, ma GitHub Pages si attiva solo con i push su `main`. Serve un merge.

Prima di procedere, Simone verifica che Python sia disponibile per MkDocs:

```bash
python3 --version
```

Se il comando restituisce una versione (es. `Python 3.12.x`), l'ambiente è pronto. MkDocs e le sue dipendenze vengono installate automaticamente dal workflow GitHub Actions, quindi non serve installare nulla localmente.

Poi Simone porta `development` su `main`:

```bash
# Simone
cd ~/progetti/RistoranteAPI
git checkout main
git pull origin main
git merge development
git push origin main
```

Il push su `main` fa partire il workflow GitHub Actions. Simone può monitorarlo:

1. Andare su **github.com/simone/RistoranteAPI/actions**
2. Vedra' il workflow "Deploy MkDocs to GitHub Pages" con indicatore giallo (in esecuzione)
3. Dopo 1-2 minuti, l'indicatore diventa verde (completato)

Il sito è live all'URL:

```
https://simonerossi.github.io/RistoranteAPI/
```

L'URL segue il pattern `https://<username>.github.io/<repository>/`.

---

## 9.8 Verifica pubblicazione

Simone apre il browser e visita l'URL. Verifica che:

- La home page si carica con il titolo "RistoranteAPI"
- La navigazione laterale mostra tutte le sezioni (Piatti, Menu, Ricette, Contribuire)
- I link tra le pagine funzionano
- Il tema Material è applicato correttamente (colori, font, layout)
- Il pulsante "torna su" funziona (configurato in `theme.features`)

Poi torna su GitHub:

- Tab **Actions**: il workflow mostra un **green checkmark** — deploy riuscito
- Tab **Settings → Pages**: mostra l'URL del sito pubblicato

Se qualcosa non funziona, il tab Actions mostra i log dettagliati di ogni step, con l'errore specifico.

---

## 9.9 Cosa è successo — dietro le quinte

Il processo completo, dal Markdown al sito pubblicato:

```
Markdown (.md)  →  mkdocs build  →  HTML statico  →  GitHub Pages
   docs/              (Python)         site/          (hosting gratuito)
```

Ogni pezzo ha un ruolo:

| Componente | Ruolo |
|---|---|
| `docs/*.md` | Il contenuto — scritto in Markdown, semplice da mantenere |
| `mkdocs.yml` | La configurazione — tema, navigazione, estensioni |
| `mkdocs build` | Il motore — trasforma Markdown in HTML usando il tema Material |
| `.github/workflows/pages.yml` | L'automazione — ricostruisce il sito ad ogni push su `main` |
| GitHub Pages | L'hosting — serve i file HTML al pubblico, gratuitamente |

Il flusso automatizzato è il punto chiave: non devi mai costruire o caricare il sito manualmente. Ogni volta che qualcuno pusha su `main`, GitHub Actions ricostruisce il sito dal Markdown aggiornato è lo pubblica. La documentazione è sempre sincronizzata con il codice.

Se domani Leonardo aggiunge una pagina, deve solo:

1. Creare il file in `docs/`
2. Aggiungerlo a `nav` in `mkdocs.yml`
3. Fare una PR verso `development`
4. Dopo il merge, portare `development` su `main`

Il sito si aggiorna da solo.

> **Nota GitLab:** Il processo completo è lo stesso (Markdown → build → HTML → hosting), ma:
> - **CI/CD**: `.gitlab-ci.yml` invece di `.github/workflows/pages.yml`
> - **Directory di output**: `public/` invece di `site/`
> - **Nome job**: deve essere `pages` (obbligatorio su GitLab)
> - **Configurazione**: non serve abilitare nulla nei Settings — se il file `.gitlab-ci.yml` ha un job `pages`, GitLab Pages si attiva automaticamente
> - **mkdocs.yml**: identico, funziona su entrambe le piattaforme

---

## 9.10 Riepilogo del corso

Da Lezione 0 a qui, abbiamo coperto l'intero ciclo di vita di un progetto Git collaborativo. Ripercorriamo il viaggio:

### Lezione 0 — Setup ambiente

Installazione di Git, configurazione iniziale (`git config`), creazione del primo repository. Tutto parte da qui.

### Lezione 1 — Creazione progetto e branch strategy

Simone crea **RistoranteAPI**, imposta il `.gitignore`, fa il primo commit e push. Si definisce la branch strategy: `main` per la produzione, `development` per lo sviluppo.

### Lezione 2 — Collaborazione (clone, collaboratori)

Leonardo fa `git clone` e diventa collaboratore. Entrambi lavorano sullo stesso repository remoto, sincronizzando con `git pull`.

### Lezione 3-4 — Worktree per lavoro isolato

`git worktree` permette di lavorare su più branch contemporaneamente, ognuno nella propria directory. Niente più `git stash` per cambiare contesto. Simone e Leonardo lavorano su feature diverse senza interferire.

### Lezione 5 — Pull Request e code review

Le PR sono il cuore della collaborazione su GitHub. Flusso: branch feature → PR → code review → approvazione → merge. Il comando `gh` (GitHub CLI) rende tutto possibile da terminale.

### Lezione 6 — Risoluzione conflitti

Quando due sviluppatori modificano le stesse righe, Git non può decidere da solo. La risoluzione dei conflitti è un merge manuale: si scelgono le modifiche corrette, si committa e si prosegue.

### Lezione 7 — Rollback (revert, reset, cherry-pick)

Gli errori capitano. `git revert` annulla un commit creandone uno nuovo. `git reset` torna indietro nella cronologia. `git cherry-pick` applica un commit specifico su un altro branch. Ogni strumento ha il suo caso d'uso.

### Lezione 8 — Hotfix di produzione

Un bug critico in produzione non può aspettare il flusso normale. L'hotfix parte da `main`, viene fixato, mergiato su `main`, e backportato su `development`. Flusso inverso rispetto al normale.

### Lezione 9 — Documentazione e GitHub Pages

La documentazione è parte del progetto. MkDocs trasforma Markdown in un sito professionale. GitHub Actions automatizza la pubblicazione. Il sito si aggiorna ad ogni push su `main`.

---

## Riepilogo comandi

```bash
# === Simone: crea branch documentazione ===
# Simone
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development
git worktree add ../RistoranteAPI-docs feature/06-documentazione
cd ../RistoranteAPI-docs

# === Creazione file (Antigravity) ===
# Crea cartella "docs" (tasto destro nel Explorer → New Folder)
# Crea i file: docs/index.md, docs/piatti.md, docs/menu.md, docs/ricette.md, docs/contribuire.md
# Crea mkdocs.yml nella root
# Crea .github/workflows/pages.yml

# === Commit e PR ===
# Simone
git add docs/ mkdocs.yml .github/
git commit -m "docs: aggiunge documentazione MkDocs e configurazione GitHub Pages"
git push -u origin feature/06-documentazione
# Crea la PR su GitHub (browser) o con: gh pr create --base development ...

# Da Antigravity Source Control (Ctrl+Shift+G):
# 1. Clicca + accanto ai file per stagearli
# 2. Scrivi il messaggio di commit nel campo in alto
# 3. Clicca Commit (Ctrl+Enter)
# 4. Clicca Sync Changes per pushare
# 5. Crea la PR su GitHub nel browser

# === Leonardo: review e merge ===
# Leonardo
gh pr review 6 --approve --body "Documentazione completa e ben strutturata. Approvo."
gh pr merge 6 --merge

# === Abilitare GitHub Pages ===
# Su GitHub: Settings → Pages → Source: GitHub Actions

# === Pubblicare su main ===
# Simone
cd ~/progetti/RistoranteAPI
git checkout main
git pull origin main
git merge development
git push origin main

# === Verifica ===
# Visitare https://simonerossi.github.io/RistoranteAPI/
# Tab Actions: verificare green checkmark
```

---

## Riepilogo generale del corso

I comandi Git essenziali che hai imparato:

| Categoria | Comando | Scopo |
|---|---|---|
| **Inizializzazione** | `git init` | Crea un nuovo repository |
| | `git clone <url>` | Scarica un repository remoto |
| **Configurazione** | `git config user.name/email` | Imposta nome e email |
| **Snapshot** | `git add <file>` | Stage delle modifiche |
| | `git commit -m "msg"` | Crea uno snapshot |
| | `git status` | Vedi lo stato del working directory |
| | `git diff` | Vedi le modifiche non committate |
| **Branch** | `git branch <nome>` | Crea un branch |
| | `git checkout <branch>` | Passa a un branch |
| | `git checkout -b <nome>` | Crea e passa a un branch |
| **Sincronizzazione** | `git push origin <branch>` | Invia commit al remoto |
| | `git pull origin <branch>` | Scarica e integra dal remoto |
| | `git fetch --all` | Scarica dati senza integrare |
| **Merge** | `git merge <branch>` | Integra un branch in quello corrente |
| **Worktree** | `git worktree add <dir> <branch>` | Crea un worktree isolato |
| | `git worktree remove <dir>` | Rimuove un worktree |
| | `git worktree list` | Elenca i worktree |
| **Cronologia** | `git log --oneline --graph` | Vedi la cronologia dei commit |
| **Rollback** | `git revert <hash>` | Annulla un commit (crea nuovo commit) |
| | `git reset --hard <hash>` | Torna a uno snapshot (perdita locale) |
| | `git cherry-pick <hash>` | Applica un commit su un altro branch |
| **GitHub CLI** | `gh pr create` | Crea una Pull Request |
| | `gh pr review <N> --approve` | Approva una PR |
| | `gh pr merge <N>` | Mergia una PR |
| | `gh pr view <N>` | Mostra dettagli di una PR |
| | `gh pr diff <N>` | Mostra il diff di una PR |
| **Cleanup** | `git branch -d <nome>` | Elimina branch locale |
| | `git push origin --delete <nome>` | Elimina branch remoto |
| | `git fetch --prune` | Pulisce riferimenti remoti obsoleti |

La regola d'oro: **commit spesso, push regolarmente, fai PR per ogni modifica significativa**. Git non è solo uno strumento tecnico — è il modo in cui un team comunica e collabora attraverso il codice.
