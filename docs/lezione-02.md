# Lezione 2 — Leonardo si unisce al progetto

**Obiettivo:** Aggiungere un collaboratore e clonare il progetto | **Tempo stimato:** 10 minuti

---

## 2.1 Simone aggiunge Leonardo come collaboratore

### Tempo: ~2 minuti

Simone ha creato il repository `RistoranteAPI` nella lezione precedente. Ora vuole far collaborare Leonardo allo stesso progetto.

**Tramite interfaccia GitHub:**

1. Simone apre il repository su GitHub: `https://github.com/SimoneRossi/RistoranteAPI`
2. Clicca su **Settings** (ultima voce del tab menu)
3. Nella sidebar sinistra, clicca su **Collaborators**
4. Clicca su **Add people**
5. Digita lo username di Leonardo: `LeonardoBianchi`
6. Seleziona il ruolo **Write** (permission = push)
7. Clicca **Add ... to this repository**

**Oppure tramite CLI:**

> Se `gh` non è installato, usa l'interfaccia web di GitHub (istruzioni sopra).

```bash
# Simone
gh api repos/SimoneRossi/RistoranteAPI/collaborators/LeonardoBianchi -X PUT -f permission=push
```

Leonardo riceve una email di invito e deve accettare:

```
GitHub Notification:
"SimoneRossi has invited you to collaborate on SimoneRossi/RistoranteAPI"

[Accept invitation]
```

Leonardo può anche accettare da terminale:

```bash
# Leonardo
gh api repos/SimoneRossi/RistoranteAPI/invitations --method GET
```

### Collaborator vs Fork

Esistono due modelli principali di collaborazione su GitHub:

| Aspetto | Collaborator (stesso repo) | Fork (copia personale) |
|---|---|---|
| Dove si lavora | Stesso repository `SimoneRossi/RistoranteAPI` | Copia `LeonardoBianchi/RistoranteAPI` |
| Accesso | Diretto al repo originale | Solo sulla propria copia |
| Permessi | Push diretto (con permission) | Serve una Pull Request |
| Caso d'uso | Team ristretto, same company | Open source, contributori esterni |

Per questo corso usiamo il modello **collaborator**: Simone e Leonardo lavorano nello stesso repository, come farebbe un piccolo team di sviluppo.

**Nota GitLab:** Su GitLab i collaboratori si aggiungono in Settings → Members (non "Collaborators"). I ruoli sono più granulari: Guest (sola lettura), Reporter (issue), Developer (push), Maintainer (merge su branch protetti), Owner (tutto). Per questo corso, il ruolo "Developer" è l'equivalente di "Write" su GitHub.

---

## 2.2 Leonardo clona il progetto

### Tempo: ~3 minuti

Leonardo ha accettato l'invito. Ora deve scaricare il repository sul suo computer.

```bash
# Leonardo
cd ~/progetti
git clone git@github.com:SimoneRossi/RistoranteAPI.git
cd RistoranteAPI
```

**In Antigravity:** Ctrl+Shift+P → "Git: Clone" → incolla l'URL del repository

Verifichiamo cosa ha scaricato:

```bash
# Leonardo
git log --oneline
```

Output atteso:

```
a1b2c3d feat: struttura iniziale progetto RistoranteAPI
e4f5g6h init: primo commit
```

```bash
# Leonardo
git branch -a
```

Output atteso:

```
* main
  remotes/origin/main
```

**Cosa fa `git clone`:**

`git clone` non scarica solo i file. Scarica **l'intera storia del progetto**:

- Tutti i commit (l'history completa)
- Tutti i branch
- I metadata del remote (chiamato automaticamente `origin`)
- Imposta il branch attivo su `main` (il branch di default)

Questo significa che Leonardo ha una copia completa e autonoma del repository. Può lavorare offline, vedere la storia, creare branch.

---

## 2.3 Leonardo si allinea sui branch

### Tempo: ~2 minuti

Nella lezione precedente Simone ha creato un branch `development`. Leonardo vuole passare su quel branch per iniziare a lavorare.

```bash
# Leonardo
git checkout development
```

Output:

```
branch 'development' set up to track 'origin/development'.
Switched to a new branch 'development'
```

Verifica:

```bash
# Leonardo
git branch
```

Output atteso:

```
  main
* development
```

Il simbolo `*` indica il branch attivo. Ora Leonardo è su `development`.

**Perché lavoriamo su `development` e non su `main`:**

- `main` rappresenta il codice stabile, pronto per la produzione
- `development` è il branch dove il team integra le nuove funzionalità
- Nessuno lavora direttamente su `main`: ogni modifica passa prima da `development`
- Questo pattern si chiama **GitHub Flow semplificato** ed è lo standard nei team piccoli

---

## 2.4 Verifica incrociata

### Tempo: ~2 minuti

Prima di iniziare a lavorare, Simone e Leonardo verificano che tutto sia allineato.

**Simone controlla la storia:**

```bash
# Simone
git log --oneline
```

Deve vedere gli stessi commit che vede Leonardo. Se coincidono, il repository e sincronizzato.

**Leonardo verifica i file:**

```bash
# Leonardo
ls -la src/
```

Oppure verifica nella sidebar di Antigravity che la cartella `src/` contiene i file `app.py`, `models.py` e `routes.py`.

Output atteso (i file creati da Simone nella lezione 1):

```
 app.py
 models.py
 routes.py
```

**Entrambi verificano il remote:**

```bash
# Simone
git remote -v
```

```
origin  git@github.com:SimoneRossi/RistoranteAPI.git (fetch)
origin  git@github.com:SimoneRossi/RistoranteAPI.git (push)
```

```bash
# Leonardo
git remote -v
```

```
origin  git@github.com:SimoneRossi/RistoranteAPI.git (fetch)
origin  git@github.com:SimoneRossi/RistoranteAPI.git (push)
```

Stesso remote, stesso repository. Entrambi puntano a `origin` che e `SimoneRossi/RistoranteAPI`.

---

## 2.5 Cosa è successo — dietro le quinte

### Tempo: ~3 minuti

### Clone vs Fork

Quando si lavora su un progetto di qualcun altro, ci sono due strade:

```
CLONE (collaborator)                    FORK (open source)
========================                ========================

GitHub: SimoneRossi/RistoranteAPI       GitHub: SimoneRossi/RistoranteAPI
          |                                        |
          | git clone                              | fork (copia su GitHub)
          v                                        v
Leonardo: ~/progetti/RistoranteAPI       GitHub: LeonardoBianchi/RistoranteAPI
  remote: origin -> SimoneRossi/            |
  push: diretto su SimoneRossi/            | git clone
                                           v
                                 Leonardo: ~/progetti/RistoranteAPI
                                  remote: origin -> LeonardoBianchi/
                                  upstream -> SimoneRossi/
                                  push: PR verso SimoneRossi/
```

**Quando usare cosa:**

- **Clone + Collaborator** — Stesso team, stessa azienda, fiducia reciproca
- **Fork + Pull Request** — Open source, contributori esterni, code review obbligatoria

**Nota GitLab:** Il concetto di fork esiste anche su GitLab ed è identico. GitLab offre anche i **fork istantanei** per i progetti interni, che sono più veloci. Il clone è identico: `git clone git@gitlab.com:SimoneRossi/RistoranteAPI.git`.

### Remote Tracking

Dopo il clone, Git configura automaticamente dei **remote-tracking branch**:

```
main               <- branch locale (HEAD)
origin/main        <- tracking branch (stato di main su GitHub)
origin/development <- tracking branch (stato di development su GitHub)
```

I remote-tracking branch (quelli con `origin/`) sono **read-only**: rappresentano lo stato del repository remoto al momento dell'ultimo `fetch` o `pull`. Non si modificano direttamente.

### git fetch

```bash
# Leonardo
git fetch
```

`git fetch` aggiorna i remote-tracking branch senza toccare i branch locali. Serve per vedere se ci sono nuovi commit su GitHub senza modificare il proprio lavoro.

Differenza chiave:

| Comando | Scarica? | Modifica il codice locale? |
|---|---|---|
| `git fetch` | Si | No (aggiorna solo `origin/*`) |
| `git pull` | Si | Si (fa fetch + merge nel branch attivo) |

---

## Riepilogo comandi

```bash
# Simone — aggiungere collaboratore
gh api repos/SimoneRossi/RistoranteAPI/collaborators/LeonardoBianchi -X PUT -f permission=push

# Leonardo — clonare il progetto
cd ~/progetti
git clone git@github.com:SimoneRossi/RistoranteAPI.git
cd RistoranteAPI

# Leonardo — esplorare il repository
git log --oneline
git branch -a

# Leonardo — passare al branch di sviluppo
git checkout development
git branch

# Entrambi — verificare allineamento
git remote -v

# Leonardo — aggiornare info remote senza modificare il codice
git fetch
```

**Alternative in Antigravity:**

| Operazione | Antigravity |
|---|---|
| Clonare | Ctrl+Shift+P → "Git: Clone" |
| Cronologia | Click sull'icona Source Control → timeline |
| Branch | Click sul branch in basso a destra nella status bar |
| Fetch / Pull | Click sull'icona Source Control → "..." → Fetch / Pull |

---

*Lezione 2 del corso Git/GitHub — Progetto RistoranteAPI*
