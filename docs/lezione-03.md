# Lezione 3 — Simone: worktree e prima feature

**Obiettivo:** Usare git worktree per lavorare su una feature in isolamento

**Tempo stimato:** 20 minuti

---

## 3.1 Perché i worktree?

Simone sta lavorando sul branch `development`. Deve iniziare una nuova feature ma vuole mantenere il branch di sviluppo pulito.

**Senza worktree** le opzioni sono:

- `git stash` per salvare il lavoro corrente, poi `git checkout` sul nuovo branch — ma perde lo stato della working directory
- `git checkout` diretto — rischiano di mescolare modifiche tra branch

**Con worktree**: più working directory dallo stesso repository, ognuna su un branch diverso.

Pensa a un worktree come avere **più scrivanie**, ognuna con un progetto diverso aperto sopra. Lavori a una scrivania senza toccare le altre.

L'insight chiave: **tutti i worktree condividono lo stesso `.git/`** — stessa history, stessi objects. Nessuna duplicazione.

---

## 3.2 Simone crea il suo worktree

Simone è sul branch `development`:

```bash
# Simone
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development
```

Crea il worktree per la feature del menu:

```bash
git worktree add ../RistoranteAPI-menu feature/01-menu-giorno
```

Questo comando:

1. Crea una **nuova directory** `../RistoranteAPI-menu`
2. Fa il checkout del branch `feature/01-menu-giorno` in quella directory
3. Il branch viene creato automaticamente se non esiste

Verifica i worktree attivi:

```bash
git worktree list
```

Output atteso:

```
/home/simone/progetti/RistoranteAPI          abc1234 [development]
/home/simone/progetti/RistoranteAPI-menu     abc1234 [feature/01-menu-giorno]
```

Controlla che il contenuto sia identico ma su un branch diverso:

```bash
ls ../RistoranteAPI-menu
```

Stessi file del repository, ma HEAD punta a un branch diverso.

---

## 3.3 Simone lavora sulla feature

Simone si sposta nel worktree della feature:

```bash
cd ../RistoranteAPI-menu
```

In Antigravity: apri `src/menu.md` e sostituisci il contenuto con:

```markdown
# Menu del Giorno

## Antipasti
* Bruschetta al Pomodoro
* Caprese con Bufala

## Primi
* Pizza Margherita DOP
* Pasta Carbonara Tradizionale
* Risotto ai Funghi Porcini

## Secondi
* Cotoletta alla Milanese
* Pesce Spada alla Griglia

## Dolci
* Tiramisu' della Nonna
* Panna Cotta ai Frutti di Bosco

## Bevande
* Acqua Naturale
* Vino della Casa
```

Salva il file (`Ctrl+S`).

Controlla le modifiche:

```bash
git diff src/menu.md
```

In Antigravity: pannello Source Control (`Ctrl+Shift+G`) → clicca sul file per vedere il diff.

Stage e commit:

```bash
git add src/menu.md
git commit -m "feat: aggiorna menu con nuove sezioni e piatti"
```

---

## 3.4 Push del branch feature

Simone pubblica il branch su GitHub:

```bash
git push -u origin feature/01-menu-giorno
```

Su GitHub: Simone può vedere il nuovo branch nella sezione_branches del repository.

Verifica che il branch remoto esista:

```bash
git branch -r
```

Output include:

```
origin/development
origin/main
origin/feature/01-menu-giorno
```

> **Nota GitLab:** Il `git push` è il concetto di worktree sono identici su GitLab. Le differenze tra GitHub e GitLab riguardano solo l'interfaccia web e i comandi CLI (`glab` invece di `gh`), non Git stesso.

---

## 3.5 Tornare al worktree principale

Simone torna al repository principale:

```bash
cd ~/progetti/RistoranteAPI
```

Verifica che il worktree principale sia ancora su `development`, senza tracce del lavoro sulla feature:

```bash
git status
```

Output:

```
On branch development
nothing to commit, working tree clean
```

**Questo è il vantaggio fondamentale**: il lavoro sulla feature è completamente isolato. Il branch `development` è pulito, senza file modificati, senza stash, senza confusione.

---

## 3.6 Gestione dei worktree

Elencare tutti i worktree:

```bash
git worktree list
```

Rimuovere un worktree (lo faremo dopo il merge):

```bash
git worktree remove ../RistoranteAPI-menu
```

**Quando rimuovere**: dopo che il branch è stato mergato. Il worktree ha fatto il suo lavoro — l'isolamento non serve più.

### Cosa succede quando rimuovi un worktree

```bash
git worktree remove ../RistoranteAPI-menu
```

Questo comando fa due cose:

1. **Elimina la directory** `RistoranteAPI-menu/` e tutti i file al suo interno — i file sorgente, il virtualenv (`.venv/`), i file `.pyc`, tutto. La directory scompare dal disco e lo spazio viene liberato.
2. **Rimuove i metadati** da `.git/worktrees/` — il puntatore HEAD, l'index e i riferimenti interni.

La storia del repository **non viene toccata**: tutti i commit, i branch e i tag restano intatti in `.git/objects/`. Rimuovere un worktree non perde nessun dato Git — elimina solo la copia di lavoro su disco.

Lo spazio liberato dipende da cosa conteneva il worktree:

| Contenuto | Spazio liberato |
|---|---|
| File sorgente tracciati da Git | Pochi KB — file di testo |
| Virtualenv (`.venv/`) | 50-500 MB — la parte più voluminosa |
| `node_modules/` | 100-500 MB nei progetti Node.js |
| File compilati (`__pycache__/`, `target/`) | Pochi MB |

Nella maggior parte dei casi, **la parte grossa dello spazio è nelle dipendenze**, non nel codice sorgente. Se il worktree non aveva dipendenze installate, la rimozione libera pochissimo spazio — i file di testo pesano poco.

**Se hai modifiche non committate** nel worktree, Git rifiuta la rimozione:

```
fatal: RistoranteAPI-menu contains modified or untracked files
```

In quel caso puoi forzare con `--force`, ma le modifiche non committate andranno perse. Meglio fare commit o stash prima di rimuovere.

**Regola importante**: NON puoi avere lo stesso branch checked out in due worktree diversi. Git lo vieterebbe.

---

## 3.7 Cosa è successo — dietro le quinte

I worktree condividono `.git/objects` e `.git/refs` — nessuna duplicazione di dati.

Ogni worktree ha il proprio:

- **HEAD** — punta al branch corretto
- **index** (staging area) — indipendente
- **working tree** — i file su disco

I metadata dei worktree si trovano in:

```bash
ls .git/worktrees/
```

In Antigravity: puoi anche ispezionare la cartella `.git/worktrees/` dall'Explorer.

La convenzione per i nomi dei branch è: `feature/NN-descrizione` dove `NN` è un numero progressivo (01, 02, 03...). Questo mantiene ordinato il repository quando le feature si accumulano.

---

## Riepilogo comandi

| Comando | Scopo |
|---|---|
| `git worktree add <percorso> <branch>` | Crea un nuovo worktree |
| `git worktree list` | Elenca tutti i worktree |
| `git worktree remove <percorso>` | Rimuove un worktree |
| `git push -u origin <branch>` | Pubblica un branch e setta upstream |
| `git branch -r` | Elenca i branch remoti |
| `git diff <file>` | Mostra le modifiche a un file |
| `git add <file>` | Stage di un file |
| `git commit -m "messaggio"` | Commit con messaggio |
