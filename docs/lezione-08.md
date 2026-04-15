# Lezione 8 — Hotfix di produzione

**Obiettivo:** Gestire un fix urgente su main e backportarlo su development

**Tempo stimato:** 15 minuti

---

## 8.1 Lo scenario: bug critico in produzione

Un cliente del ristorante segnala che sul menu online mancano le informazioni sugli allergeni. In Italia questo è un **requisito di legge** — il ristorante rischia una multa.

Il problema è su `main`, il branch di produzione. Non possiamo aspettare il normale ciclo di sviluppo: il menu è già pubblico e ogni ora senza allergeni è un rischio.

Serve un **hotfix**: un fix rapido applicato direttamente su `main`, senza passare da `development`.

---

## 8.2 La branch strategy con hotfix

La situazione attuale del repository:

```
main ──────────────────────────────── hotfix/fix-allergeni ──●
 │                                                              │
 └─ development ───────────────────────────────────── backport ─●
     │
     ├─ feature/...
     └─ feature/...
```

Due regole fondamentali:

1. **L'hotfix branch parte da `main`** — non da `development`. Il bug è in produzione e va fixato li.
2. **Dopo il fix, merge su `main` E backport su `development`** — altrimenti il prossimo deploy sovrascrive il fix.

Il flusso normale è `feature → development → main`. L'hotfix va nella direzione opposta: `main → development`. Per questo è un'eccezione controllata.

---

## 8.3 Simone crea l'hotfix

Simone sta lavorando su una feature nel suo worktree sul server Linux. Deve cambiare contesto rapidamente senza abbandonare il lavoro corrente.

Il worktree è perfetto qui: crea una directory separata per l'hotfix senza toccare il worktree della feature. Simone usa Antigravity IDE collegato al server via Remote-SSH.

```bash
# Simone
cd ~/progetti/RistoranteAPI
git fetch --all
```

Prima tenta di creare il worktree:

```bash
git worktree add ../RistoranteAPI-hotfix hotfix/fix-allergeni
```

Errore! Il branch `hotfix/fix-allergeni` non esiste ancora. Deve crearlo basandolo su `main`:

```bash
git worktree add ../RistoranteAPI-hotfix -b hotfix/fix-allergeni origin/main
```

Cosa fa ogni parte:

| Parte | Significato |
|---|---|
| `../RistoranteAPI-hotfix` | Directory del nuovo worktree |
| `-b hotfix/fix-allergeni` | Crea il branch con questo nome |
| `origin/main` | Punto di partenza: il branch di produzione |

Si sposta nel nuovo worktree:

```bash
cd ../RistoranteAPI-hotfix
```

Verifica di essere basato su `main`:

```bash
git log --oneline -3
```

L'output deve mostrare i commit di `main`, non quelli di `development`. Questo conferma che l'hotfix è nato dal branch giusto.

---

## 8.4 Il fix

In Antigravity: apri `src/menu.md` e sostituisci il contenuto con:

```markdown
# Menu del Giorno

## Antipasti
* Bruschetta al Pomodoro _(contiene: glutine)_
* Caprese con Bufala _(contiene: lattosio)_

## Primi
* Pizza Margherita DOP _(contiene: glutine, lattosio)_
* Pasta Carbonara Tradizionale _(contiene: glutine, uova, lattosio)_
* Risotto ai Funghi Porcini _(contiene: nessun allergene principale)_

## Secondi
* Cotoletta alla Milanese _(contiene: glutine, uova, lattosio)_
* Pesce Spada alla Griglia _(contiene: pesce)_

## Dolci
* Tiramisu' della Nonna _(contiene: uova, lattosio, glutine)_
* Panna Cotta ai Frutti di Bosco _(contiene: lattosio)_

## Bevande
* Acqua Naturale
* Vino della Casa _(contiene: solfiti)_

---
> ⚠️ **Avvertenza allergeni**: tutti i piatti possono contenere tracce di frutta a guscio.
```

Commit e push:

```bash
git add src/menu.md
git commit -m "hotfix: aggiunge informazioni allergeni al menu"
git push -u origin hotfix/fix-allergeni
```

Il messaggio di commit inizia con `hotfix:` — questa è la convenzione per distinguere i fix urgenti dai commit normali.

---

## 8.5 PR diretta su main

L'hotfix bypassa `development` e va direttamente su `main`. Questo è l'unico caso in cui una PR non segue il flusso `feature → development → main`.

```bash
# Simone
gh pr create --base main --head hotfix/fix-allergeni \
  --title "hotfix: Informazioni allergeni nel menu" \
  --body "## Urgenza
Un cliente ha segnalato la mancanza di allergeni sul menu.

## Modifiche
- Aggiunte info allergeni a ogni piatto
- Aggiunta avvertenza generale

## Test
- [x] Verificato tutti gli allergeni corretti"
```

> **Nota:** se `gh` non è installato sul server, crea la PR dalla web UI di GitHub: vai su **Pull Requests** → **New pull request** → imposta base `main` e compare `hotfix/fix-allergeni`.

Leonardo riceve la notifica e revisiona velocemente:

```bash
# Leonardo
gh pr review 5 --approve --body "Fix urgente approvato. Allergeni corretti."
```

Simone effettua il merge:

```bash
# Simone
gh pr merge 5 --merge
```

Il fix è ora su `main`. Il menu in produzione mostra gli allergeni. Ma `development` è ancora indietro.

> **Nota GitLab:** Su GitLab la Merge Request per l'hotfix si crea nello stesso modo: `glab mr create --target-branch main --source-branch hotfix/fix-allergeni`. L'hotfix bypass il branch di sviluppo e va direttamente su main — il concetto è identico.

---

## 8.6 Backport su development

Se non backportiamo il fix, il prossimo merge da `development` verso `main` sovrascriverebbe le modifiche. `development` deve ricevere lo stesso commit.

Il flusso è inverso rispetto al normale: invece di mergiare `development` in `main`, mergiamo `main` in `development`.

```bash
# Simone
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development
git merge origin/main
git push origin development
```

Verifica con il grafo:

```bash
git log --oneline --graph -5
```

L'output mostra un merge commit che porta il contenuto di `main` dentro `development`. Ora i due branch sono sincronizzati su questo fix.

Il concetto chiave: **normalmente le feature fluiscono da `dev` verso `main`**. Gli hotfix fluiscono nella direzione opposta: da `main` verso `dev`. Questo è il backport.

---

## 8.7 Cleanup

Simone rimuove il worktree temporaneo e pulisce i branch:

```bash
# Simone
git worktree remove ../RistoranteAPI-hotfix
git fetch --prune
git branch -d hotfix/fix-allergeni
```

| Comando | Cosa fa |
|---|---|
| `git worktree remove` | Elimina la directory del worktree e libera lo spazio su disco |
| `git fetch --prune` | Rimuove i riferimenti a branch remoti cancellati |
| `git branch -d` | Elimina il branch locale |

La scrivania dell'hotfix è stata smontata. Simone torna al suo lavoro sulla feature.

---

## 8.8 Cosa è successo — dietro le quinte

L'hotfix è un workflow speciale per fix urgenti in produzione. Ecco le differenze rispetto al flusso normale:

| Aspetto | Flusso normale | Hotfix |
|---|---|---|
| **Branch di partenza** | `development` | `main` |
| **Target della PR** | `development` | `main` |
| **Naming** | `feature/descrizione` | `hotfix/descrizione` |
| **Backport necessario?** | No | Si, verso `development` |
| **Urgenza** | Normale | Critica |

**Alternativa al merge per il backport:** `git cherry-pick` permette di applicare un singolo commit su un altro branch, senza fare un merge completo:

```bash
git checkout development
git cherry-pick abc1234
```

Dove `abc1234` è l'hash del commit dell'hotfix su `main`. Il cherry-pick è utile quando `development` ha divergento molto da `main` è un merge completo sarebbe complesso.

**Su progetti reali** le PR di hotfix possono bypassare alcuni check CI o requisiti di review — ma solo con approvazione esplicita. L'urgenza non dovrebbe mai significare "salto la qualità'".

> **Nota GitLab:** Il workflow di hotfix è identico. GitLab permette anche di creare MR con **target multipli** in alcuni casi, ma il flusso main → backport su development è lo stesso. L'uso di worktree per l'hotfix è puramente Git e funziona identicamente.

---

## Riepilogo comandi

| Comando | Scopo |
|---|---|
| `git fetch --all` | Scarica tutti i dati dal remote |
| `git worktree add <percorso> -b <branch> origin/main` | Crea worktree con nuovo branch basato su main (es. `../RistoranteAPI-hotfix`) |
| `git log --oneline -3` | Verifica i commit recenti |
| `git add src/menu.md` | Stage delle modifiche |
| `git commit -m "hotfix: ..."` | Commit con prefisso hotfix |
| `git push -u origin hotfix/fix-allergeni` | Pubblica il branch |
| `gh pr create --base main --head hotfix/...` | PR diretta su main |
| `gh pr review 5 --approve` | Approvazione rapida |
| `gh pr merge 5 --merge` | Merge della PR |
| `git checkout development` | Passa a development |
| `git pull origin development` | Aggiorna development locale |
| `git merge origin/main` | Backport: merge di main in development |
| `git push origin development` | Pubblica development aggiornato |
| `git log --oneline --graph -5` | Verifica il grafo dei commit |
| `git worktree remove <percorso>` | Rimuove il worktree (es. `../RistoranteAPI-hotfix`) |
| `git fetch --prune` | Pulisce i riferimenti remoti obsoleti |
| `git branch -d <branch>` | Elimina il branch locale |
| `git cherry-pick <hash>` | Applica un singolo commit su un altro branch |

---

*Prossima lezione: tag, release e versionamento semantico.*
