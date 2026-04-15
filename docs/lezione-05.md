# Lezione 5 — La prima Pull Request

**Obiettivo:** Creare una PR, fare code review, approvare e mergere

**Tempo stimato:** 15 minuti

---

## 5.1 Che cos'e' una Pull Request (PR)?

Una **Pull Request** è una richiesta di unire cambiamenti da un branch a un altro. Simone ha completato il suo lavoro su `feature/01-menu-giorno` e vuole integrarlo in `development`.

**Attenzione:** una PR **non è una funzionalità di Git**. E' una funzionalità di **GitHub** (e di piattaforme simili come GitLab o Bitbucket). Il comando `git merge` esiste in Git, ma il concetto di "pull request" con review, discussione e controlli automatizzati è specifico della piattaforma.

### Perché le PR sono importanti

Senza PR, l'unico modo per integrare codice è fare un merge diretto. Nessuna discussione, nessuna revisione, nessun controllo. Con le PR:

- **Code review** — qualcuno legge il codice prima che venga integrato
- **Discussione** — si possono lasciare commenti e fare domande
- **CI checks** — test automatizzati girano prima del merge
- **Tracciabilita'** — ogni cambiamento è documentato con contesto

### Il flusso

```
1. Creare la PR    (Simone chiede di mergiare)
2. Review          (Leonardo legge il codice)
3. Approvare       (Leonardo dice "ok, va bene")
4. Merge           (le modifiche entrano in development)
```

### Chi fa cosa? I ruoli nelle PR

GitHub non impone regole rigide su chi fa cosa. Chiunque abbia **accesso in scrittura** al repository (nel nostro caso, sia Simone che Leonardo) può creare PR, approvarle e mergiarle. Ma ci sono delle **convenzioni** che un team serio segue:

| Azione | Chi | Perché |
|---|---|---|
| **Creare la PR** | Chi ha scritto il codice (l'autore) | E' la richiesta: "Ecco il mio lavoro, per favore integratelo" |
| **Fare la review** | Un'altra persona (NON l'autore) | Quattro occhi vedono meglio di due. Chi ha scritto il codice è troppo vicino per vedere i propri errori |
| **Approvare** | Chi ha fatto la review | L'approvazione significa: "Ho letto, ho capito, e mi sta bene" |
| **Mergiare** | Chiunque (dopo l'approvazione) | Spesso è l'autore, a volte è il reviewer, a volte un maintainer. Non è un passaggio critico: l'approvazione è il vero filtro |

**Nota GitLab:** Su GitLab le Pull Request si chiamano **Merge Request (MR)**. Il concetto è identico: una richiesta di unire un branch in un altro, con review, discussione e controlli. La terminologia cambia (MR invece di PR) ma il flusso di lavoro è lo stesso. Nei comandi CLI: `glab mr create` invece di `gh pr create`, `glab mr merge` invece di `gh pr merge`.

**La regola fondamentale:** non approvare MAI la tua stessa PR. Se Simone crea una PR, la deve approvare Leonardo (e viceversa). Il punto delle PR è proprio questo: un'altra persona controlla il tuo lavoro.

**Cosa succede se approvi la tua PR?** GitHub lo permette (in un repo senza branch protection), ma è come farsi i compiti da soli e mettersi il voto. Sconfigge lo scopo della code review.

**Nei progetti reali** si impostano le **branch protection rules** (Settings → Branches → Branch protection rules) per rendere queste convenzioni obbligatorie: GitHub rifiuta il merge se non c'e' almeno un'approvazione da un'altra persona. Per questo corso non le attiviamo, ma seguiremo la regola: **l'autore crea, l'altro approva**.

Nel nostro progetto:
- Simone crea la PR → Leonardo fa review e approva → Simone (o Leonardo) mergia
- Leonardo crea la PR → Simone fa review e approva → Leonardo (o Simone) mergia

---

## 5.2 Simone crea la PR

### Prerequisito: il branch deve essere su GitHub

Una PR è una funzionalità di **GitHub**, non di Git. GitHub può confrontare solo branch che esistono **sul server remoto**. Un branch che esiste solo sul tuo computer è invisibile a GitHub.

Questo significa che **prima di creare una PR devi aver pushato il branch**:

```
git commit → modifiche salvate LOCALMENTE
git push   → branch visibile SU GITHUB → ORA puoi creare la PR
```

Se tenti di creare una PR per un branch che non è stato pushato, GitHub non lo trovera'. Il comando `git push -u origin feature/01-menu-giorno` (lezione 3) ha già fatto questo lavoro.

In sintesi: **commit = salvo localmente, push = rendo visibile a GitHub, PR = chiedo di integrare**.

---

La feature è pronta, il branch è stato pubblicato. Simone vuole mergiare in `development`.

```bash
# Simone
cd ~/progetti/RistoranteAPI-menu
```

### Via GitHub CLI

Se `gh` CLI è installato:

```bash
# Simone
gh pr create \
  --base development \
  --head feature/01-menu-giorno \
  --title "feat: Aggiorna menu con nuove sezioni" \
  --body "## Modifiche
- Aggiunte sezioni Antipasti, Dolci, Bevande
- Aggiornati i nomi dei piatti con descrizioni
- Riorganizzato il layout del menu

## Testing
- [x] Verificato contenuto menu.md
- [x] Nessun conflitto con development"
```

In bash, `\` è il carattere di continuazione di riga. In alternativa, scrivi tutto su una riga.

Ogni flag ha un ruolo preciso:

| Flag | Significato |
|---|---|
| `--base development` | Il branch di destinazione (dove mergiare) |
| `--head feature/01-menu-giorno` | Il branch sorgente (le modifiche da integrare) |
| `--title` | Il titolo della PR — segue la convenzione `tipo: descrizione` |
| `--body` | La descrizione dettagliata — può usare Markdown |

Il body della PR è importante. Una buona descrizione include: cosa è cambiato, perché, è come verificare. Le checkbox `[x]` indicano i test già fatti.

L'output del comando sarà qualcosa del genere:

```
Creating pull request for feature/01-menu-giorno into development in simone/RistoranteAPI

https://github.com/simone/RistoranteAPI/pull/1
```

GitHub assegna un numero progressivo a ogni PR. Questa è la **PR #1** del repository.

### Via Antigravity

Antigravity eredita da VS Code l'integrazione nativa con GitHub. Dopo aver pushato il branch, vedi un popup o un messaggio nella status bar che invita a creare una PR. Puoi anche usare:

1. **Ctrl+Shift+P** → digita "GitHub Pull Requests: Create Pull Request"
2. Oppure clicca sull'icona **GitHub** nella sidebar sinistra (l'icona con il simbolo di GitHub) → **Create Pull Request**
3. Antigravity mostra un form dove compilare:
   - **Base**: `development` (branch di destinazione)
   - **Head**: `feature/01-menu-giorno` (già selezionato, è il branch corrente)
   - **Title**: "feat: Aggiorna menu con nuove sezioni"
   - **Description**: la descrizione della PR (supporta Markdown)
4. Clicca **Create**

Il vantaggio: resti nell'editor senza aprire il browser o il terminale. L'estensione GitHub Pull Requests deve essere installata in Antigravity (di solito è già inclusa).

### Via GitHub web UI

Alternativa via browser:

1. Vai su **github.com/simone/RistoranteAPI**
2. GitHub mostra un banner giallo: "feature/01-menu-giorno had recent pushes" — clicca **Compare & pull request**
3. Oppure: tab **Pull requests** → **New pull request** → seleziona `base: development` e `compare: feature/01-menu-giorno`
4. Compila titolo e descrizione
5. Clicca **Create pull request**

---

## 5.3 La PR su GitHub — tour

Simone apre la PR nel browser:

```
https://github.com/simone/RistoranteAPI/pull/1
```

La pagina della PR ha diverse sezioni:

### Tab Conversation

La discussione principale. Mostra:
- Titolo e descrizione della PR
- Thread di commenti (in basso)
- Stato dei controlli automatizzati (se configurati)
- Pulsante **Merge pull request** (attivo dopo l'approvazione)

### Tab Commits

La lista di tutti i commit inclusi nella PR. In questo caso, un solo commit:

```
feat: aggiorna menu con nuove sezioni e piatti
```

### Tab Files Changed

Il cuore della code review. Mostra il **diff** — cioè le differenze tra `development` e `feature/01-menu-giorno`:

- **Linee rosse** (con `-`) = righe rimosse o modificate
- **Linee verdi** (con `+`) = righe aggiunte

```
- # Menu del Giorno
-
- * Pizza Margherita
- * Pasta Carbonara
- * Tiramisù
+ # Menu del Giorno
+
+ ## Antipasti
+ * Bruschetta al Pomodoro
+ * Caprese con Bufala
...
```

Questo è cio' che Leonardo recensira'.

### Tab Checks

Se il repository ha CI/CD configurato (GitHub Actions, per esempio), qui si vedono i risultati dei test. Per ora il nostro progetto non ha CI, quindi sarà vuoto.

---

## 5.4 Leonardo fa code review

Leonardo riceve una notifica (email o notifica GitHub) che Simone ha aperto una PR. Oppure controlla manualmente.

### Via GitHub web UI

1. Leonardo apre la PR #1 su GitHub
2. Va al tab **Files changed**
3. Legge ogni modifica riga per riga
4. Può cliccare sul simbolo `+` blu accanto a qualsiasi riga per lasciare un **commento inline**
5. Può fare domande, suggerire modifiche, o segnalare problemi

Per lasciare una review completa:

1. Dopo aver letto tutto, clicca **Review changes** (in alto a destra)
2. Sceglie un'opzione:
   - **Comment** — commento generico, senza approvare o rifiutare
   - **Approve** — il codice è ok, si può mergiare
   - **Request changes** — servono correzioni prima del merge
3. Scrive un messaggio di review
4. Clicca **Submit review**

### Via Antigravity IDE

Leonardo può anche usare l'estensione **GitHub Pull Requests** in Antigravity (se installata) per fare review direttamente dall'IDE, senza aprire il browser. L'estensione permette di:

- Vedere la lista delle PR nella sidebar
- Leggere il diff con l'evidenziazione della sintassi
- Lasciare commenti inline e approvare/richiedere modifiche

### Via CLI

Se `gh` è installato, Leonardo può anche fare review da terminale:

```bash
# Leonardo
cd ~/progetti/RistoranteAPI
```

Vedere i dettagli della PR:

```bash
# Leonardo
gh pr view 1
```

Output:

```
feat: Aggiorna menu con nuove sezioni #1
Open • simone wants to merge 1 commit into development from feature/01-menu-giorno

## Modifiche
- Aggiunte sezioni Antipasti, Dolci, Bevande
- Aggiornati i nomi dei piatti con descrizioni
- Riorganizzato il layout del menu
...
```

Vedere il diff dalla CLI:

```bash
# Leonardo
gh pr diff 1
```

L'output è lo stesso diff che si vede nel tab "Files changed" su GitHub — le modifiche riga per riga.

Leonardo approva la PR:

```bash
# Leonardo
gh pr review 1 --approve --body "Ottimo lavoro! Il menu e' ben organizzato. Approvo."
```

Il flag `--approve` imposta la review come approvazione. Se Leonardo avesse voluto richiedere modifiche, avrebbe usato `--request-changes`.

**Nota GitLab:** La code review su GitLab funziona in modo simile. Le opzioni di review sono le stesse (Approve, Request changes). GitLab supporta anche le **approval rules**: puoi richiedere che N persone approvino prima del merge, o che l'approvazione arrivi da ruoli specifici. Si configura in Settings → Merge Requests → Approval rules.

---

## 5.5 Simone mergia la PR

Dopo l'approvazione di Leonardo, la PR può essere mergiata. Può farlo chiunque abbia i permessi — Simone, Leonardo, o un maintainer.

**Via CLI:**

```bash
# Simone
gh pr merge 1 --merge
```

GitHub chiedera' conferma e poi effettuera' il merge. L'output:

```
Merging pull request #1 (feat: Aggiorna menu con nuove sezioni)
✔ Merged pull request #1 (feat: Aggiorna menu con nuove sezioni)
```

**Via GitHub web UI:** clicca **Merge pull request** sulla pagina della PR.

### Le tre opzioni di merge

Quando mergi una PR, GitHub offre tre strategie:

| Strategia | Cosa fa | Quando usarla |
|---|---|---|
| **Merge commit** | Preserva tutti i commit e crea un commit di merge aggiuntivo | Quando la cronologia dei commit è importante (default per questo corso) |
| **Squash and merge** | Combina tutti i commit della PR in un unico commit | Quando i commit sono tanti e disordinati — produce una cronologia pulita |
| **Rebase and merge** | Riproduce i commit sulla cima del branch target (storia lineare) | Quando si vuole una cronologia lineare senza commit di merge |

Per questo corso usiamo **Merge commit** — è la strategia più semplice da capire e lascia intatta la cronologia.

**Nota GitLab:** GitLab offre le stesse tre strategie più una quarta: **Fast-forward merge** — applica i commit senza creare un merge commit, ma solo se possibile (history lineare). GitLab permette anche di impostare la strategia di merge predefinita a livello di progetto (Settings → Merge Requests → Merge method).

---

## 5.6 Sincronizzare development

Dopo il merge su GitHub, il branch remoto `origin/development` contiene le modifiche di Simone. Ma le copie locali sono **indietro** — non sanno ancora del merge.

Entrambi gli sviluppatori aggiornano il proprio `development` locale:

```bash
# Simone
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development
```

```bash
# Leonardo
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development
```

`git pull` scarica i nuovi commit dal remoto è lì integra nel branch locale. Ora entrambi hanno la versione aggiornata di `development`.

Verifica la cronologia:

```bash
git log --oneline --graph
```

Output (su entrambi i computer):

```
*   abc5678 Merge pull request #1 from simone/feature/01-menu-giorno
|\
| * def1234 feat: aggiorna menu con nuove sezioni e piatti
|/
* ghi9012 ...
```

Il grafo mostra il **merge commit** — il punto in cui i due branch si sono riuniti. I due commit figli (`ghi9012` e `def1234`) si fondono nel commit di merge (`abc5678`).

---

## 5.7 Cleanup del worktree

La feature è stata mergiata. Il branch `feature/01-menu-giorno` ha fatto il suo lavoro e non serve più.

Simone pulisce tutto — worktree, branch locale e branch remoto:

```bash
# Simone
git worktree remove ../RistoranteAPI-menu
```

Rimuove la directory del worktree. I file fisici vengono eliminati e lo spazio su disco viene liberato — inclusi eventuali virtualenv o dipendenze installate nel worktree (vedi lezione 3 per i dettagli).

```bash
# Simone
git branch -d feature/01-menu-giorno
```

Il flag `-d` (minuscolo) elimina il branch locale **solo se è già stato mergiato**. Se il branch non fosse stato integrato, Git rifiuterebbe l'eliminazione per sicurezza. Per forzare l'eliminazione di un branch non mergiato si usa `-D` (maiuscolo).

```bash
# Simone
git push origin --delete feature/01-menu-giorno
```

Elimina il branch dal repository remoto su GitHub. Dopo questo comando, il branch non esiste più da nessuna parte — locale è remoto sono puliti.

**Nota:** GitHub può eliminare automaticamente i branch dopo il merge se l'opzione è abilitata (Settings → General → "Automatically delete head branches"). In quel caso, il passaggio `git push origin --delete` non sarebbe necessario.

Verifica che il cleanup sia completo:

```bash
git worktree list
git branch -a
```

L'output dovrebbe mostrare solo il worktree principale e i branch `development` e `main` — nessuna traccia della feature completata.

---

## 5.8 Cosa è successo — dietro le quinte

Ricapitoliamo l'intero processo:

### Il merge commit

Quando Simone ha eseguito `gh pr merge 1 --merge` (o cliccato Merge pull request su GitHub), GitHub ha creato un **merge commit** sul branch `development`. Questo commit ha due genitori:

1. Il commit precedente di `development`
2. L'ultimo commit di `feature/01-menu-giorno`

Il risultato: `development` ora contiene tutte le modifiche di Simone.

### La sincronizzazione

Prima del `git pull`, il `development` locale di Leonardo era indietro rispetto al remoto. Il `git pull` ha scaricato il merge commit e tutti i commit della feature, aggiornando la copia locale.

```
GitHub (remoto)             Leonardo (locale)
development: A-B-M          development: A-B
                            ↓ git pull
feature:    C               development: A-B-M
                            (M = merge commit)
```

### Branch protection

Nei progetti reali, si possono impostare le **branch protection rules** su GitHub:

- Richiedere almeno una review approvata prima del merge
- Richiedere che i CI checks passino
- Vietare il push diretto su `main` o `development`
- Richiedere che il branch sia aggiornato prima del merge

Si configurano in **Settings → Branches → Branch protection rules**. Per ora non le attiviamo, ma è bene sapere che esistono.

**Nota GitLab:** Le branch protection rules su GitLab si configurano in Settings → Repository → Protected branches. GitLab offre più opzioni: puoi specificare chi può pushare, chi può mergiare, e se il merge richiede approvazioni. Il concetto è identico, cambia solo dove si configura.

---

## Riepilogo comandi

```
# === Simone: crea la PR ===

# Via GitHub CLI (se gh e' installato):
# Simone
cd ~/progetti/RistoranteAPI-menu
gh pr create \
  --base development \
  --head feature/01-menu-giorno \
  --title "feat: Aggiorna menu con nuove sezioni" \
  --body "## Modifiche
- Aggiunte sezioni Antipasti, Dolci, Bevande
- Aggiornati i nomi dei piatti con descrizioni
- Riorganizzato il layout del menu"

# Via GitHub web UI (se gh non e' installato):
#   github.com/simone/RistoranteAPI → Compare & pull request

# === Leonardo: code review ===

# Via GitHub web UI:
#   Apri PR #1 → tab Files changed → Review changes → Approve

# Via Antigravity IDE (se estensione GitHub PR installata):
#   Sidebar GitHub → PR #1 → Review → Approve

# Via CLI (se gh e' installato):
# Leonardo
cd ~/progetti/RistoranteAPI
gh pr view 1
gh pr diff 1
gh pr review 1 --approve --body "Ottimo lavoro! Il menu e' ben organizzato. Approvo."

# === Simone: merge ===

# Via CLI:
# Simone
gh pr merge 1 --merge

# Via GitHub web UI: clicca Merge pull request sulla pagina della PR

# === Entrambi: sincronizzazione ===
# Simone
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development
# Leonardo
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development

# === Simone: cleanup ===
# Simone
git worktree remove ../RistoranteAPI-menu
git branch -d feature/01-menu-giorno
git push origin --delete feature/01-menu-giorno

# === Verifica ===
git worktree list
git branch -a
git log --oneline --graph
```
