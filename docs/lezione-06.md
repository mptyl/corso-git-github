# Lezione 6 — Conflitto di merge!

**Obiettivo:** Gestire un merge conflict tra due sviluppatori

**Tempo stimato:** 25 minuti

---

## 6.1 Il conflitto si prepara

Dopo la lezione 5, il branch `development` contiene le modifiche al menu di Simone, mergiate tramite Pull Request. Tutto funziona. Ma c'e' un problema in arrivo.

La situazione attuale:

- **Simone** ha mergiato le sue modifiche a `src/menu.md` in `development` (lezione 5)
- **Leonardo** ha creato il suo branch `feature/02-piatti-speciali` **prima** del merge di Simone
- Leonardo ha gia' aggiunto la ricetta dei ravioli a `src/ricette.md` (lezione 4)

Fin qui nessun problema: Simone non ha toccato `ricette.md` nella sua PR. Ma ora **entrambi** vogliono modificare `ricette.md` — e lo faranno in punti diversi del file, aggiungendo contenuto nuovo.

Questo e' il classico scenario che genera un **merge conflict**.

```
Stato di ricette.md su development (dopo merge di Simone):
┌──────────────────────────────────────┐
│ # Ricette                            │
│                                      │
│ ## Pizza Margherita                  │
│ Ingredienti: farina, pomodoro...     │
│                                      │
│ ## Pasta Carbonara                   │
│ Ingredienti: spaghetti, guanciale... │
└──────────────────────────────────────┘

Simone aggiungera' qui sotto →  ricette dolci
Leonardo aggiungera' qui sotto →  ricette speciali
```

Pensa a due persone che scrivono sulla stessa pagina di un quaderno, ognuna nella propria meta'. Quando cercano di unire le pagine, bisogna decidere come combinarle.

---

## 6.2 Simone modifica ricette.md

Simone parte dal `development` aggiornato. Crea un nuovo worktree per la sua feature:

```bash
# Simone
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development
git worktree add ../RistoranteAPI-ricette feature/03-ricette-dolci
cd ../RistoranteAPI-ricette
```

Simone aggiunge una sezione dolci a `src/ricette.md`.

In Antigravity: apri `src/ricette.md` dal Explorer e sostituisci il contenuto. Salva con Ctrl+S.

Il contenuto completo del file:

```markdown
# Ricette

## Pizza Margherita
Ingredienti: farina, pomodoro, mozzarella, basilico

## Pasta Carbonara
Ingredienti: spaghetti, guanciale, uova, pecorino

## Tiramisu'
Ingredienti: mascarpone, savoiardi, caffe, uova, cacao
Preparazione: montare tuorli con zucchero, aggiungere mascarpone, inzuppare savoiardi nel caffe, alternare strati, spolverare con cacao

## Panna Cotta
Ingredienti: panna, zucchero, colla di pesce, vaniglia
Preparazione: scaldare la panna con zucchero e vaniglia, aggiungere colla di pesce ammorbidita, versare negli stampini, raffreddare in frigo 4 ore
```

Simone ha aggiunto **Tiramisu'** e **Panna Cotta** alla fine del file. Commit e push:

```bash
git add src/ricette.md
git commit -m "feat: aggiunge ricette dolci (tiramisu e panna cotta)"
git push -u origin feature/03-ricette-dolci
```

---

## 6.3 Leonardo aggiorna la sua branch

Leonardo lavora nel suo worktree `feature/02-piatti-speciali`, creato prima del merge di Simone. Ha gia' la ricetta dei ravioli. Ora vuole aggiungere anche una sezione antipasti:

```bash
# Leonardo
cd ~/progetti/RistoranteAPI-piatti
```

Leonardo modifica `src/ricette.md`.

In Antigravity: apri `src/ricette.md` dal Explorer e sostituisci il contenuto. Salva con Ctrl+S.

Il contenuto completo del file:

```markdown
# Ricette

## Pizza Margherita
Ingredienti: farina, pomodoro, mozzarella, basilico

## Pasta Carbonara
Ingredienti: spaghetti, guanciale, uova, pecorino

## Ravioli di Ricotta e Spinaci
Ingredienti: farina, ricotta, spinaci, noce moscata, burro, salvia
Preparazione: impastare la farina con le uova, preparare il ripieno con ricotta e spinaci, formare i ravioli, cuocere in acqua salata, condire con burro e salvia

## Bruschetta al Pomodoro
Ingredienti: pane casereccio, pomodori ciliegino, aglio, basilico, olio extravergine
Preparazione: tostare il pane, strofinare l'aglio, tagliare i pomodorini, condire con olio e basilico
```

Leonardo ha aggiunto **Ravioli** e **Bruschetta**. Commit e push:

```bash
git add src/ricette.md
git commit -m "feat: aggiunge ricetta bruschetta e aggiorna ravioli"
git push -u origin feature/02-piatti-speciali
```

Ora entrambi hanno modificato `ricette.md` in modi diversi. Il conflitto e' pronto.

---

## 6.4 Simone crea la PR — niente conflitto

Simone apre la Pull Request per primo:

```bash
# Simone
gh pr create --base development --head feature/03-ricette-dolci \
  --title "feat: Aggiunge ricette dei dolci" \
  --body "Aggiunte ricette di Tiramisu e Panna Cotta"
```

Questa PR si mergea senza problemi. Perche'? Perche' `development` non e' cambiato dall'ultima volta che Simone ha fatto pull — la branch di Leonardo non e' ancora stata mergiata.

Leonardo fa la review:

```bash
# Leonardo
gh pr review 2 --approve --body "Ricette corrette, approvo."
```

E Simone mergea:

```bash
# Simone
gh pr merge 2 --merge
```

Ora `development` contiene le ricette dolci. La prossima PR non sara' cosi' fortunata.

---

## 6.5 Leonardo crea la PR — CONFLITTO!

Leonardo apre la sua PR:

```bash
# Leonardo
gh pr create --base development --head feature/02-piatti-speciali \
  --title "feat: Piatti speciali e nuove ricette" \
  --body "Aggiornamento catalogo piatti con sezione speciali, aggiunta ricetta ravioli e bruschetta"
```

GitHub mostra un messaggio rosso:

```
This branch has conflicts that must be resolved
```

**Perche?** Leonardo ha modificato `ricette.md` partendo da una versione che **non includeva** le ricette dolci di Simone. Nel frattempo, Simone ha mergiato la sua PR che aggiungeva contenuto alla fine dello stesso file. Git non sa quale versione tenere: entrambe hanno aggiunto righe nuove nella stessa zona del file.

Pensa a due autori che riscrivono lo stesso capitolo di un libro senza leggersi a vicenda. Quando l'editore deve unire le due versioni, deve decidere cosa tenere.

E' importante capire che **il conflitto non e' un errore** — e' Git che ti dice: "Ho due versioni diverse dello stesso punto del file e non posso decidere da solo quale e' quella giusta."

> **Nota GitLab:** Su GitLab il conflitto viene segnalato nello stesso modo nella Merge Request. L'interfaccia web di GitLab offre un editor di conflitti integrato simile a quello di GitHub. La risoluzione locale con `git merge origin/development` e la modifica manuale dei conflict markers e' identica su entrambe le piattaforme — e' un'operazione di Git, non della piattaforma.

---

## 6.6 Risoluzione del conflitto

Ci sono due modi per risolvere un merge conflict:

- **Opzione A:** Risolvi su GitHub (web editor — comodo per conflitti semplici)
- **Opzione B:** Risolvi localmente (consigliato per progetti reali)

Leonardo risolve localmente. Prima porta le modifiche di `development` nella sua branch:

```bash
# Leonardo
cd ~/progetti/RistoranteAPI-piatti
git fetch origin
git merge origin/development
```

Quando c'e' un conflitto, Antigravity evidenzia il file con un'icona di avvertimento nel Explorer. Cliccando sul file, Antigravity mostra un'interfaccia visiva per la risoluzione del conflitto con pulsanti: "Accept Current Change", "Accept Incoming Change", "Accept Both Changes". Per questo esercizio, scegliamo di risolvere manualmente per capire esattamente cosa sta succedendo. Nella pratica quotidiana, i pulsanti di Antigravity sono il modo piu' rapido per risolvere conflitti semplici.

Git segnala il conflitto:

```
Auto-merging src/ricette.md
CONFLICT (content): Merge conflict in src/ricette.md
Automatic merge failed; fix conflicts and then commit the result.
```

Aprendo `src/ricette.md`, Leonardo trova i **conflict markers** — linee speciali che Git inserisce per mostrare le due versioni:

```
<<<<<<< HEAD
## Ravioli di Ricotta e Spinaci
Ingredienti: farina, ricotta, spinaci, noce moscata, burro, salvia
Preparazione: impastare la farina con le uova, preparare il ripieno con ricotta e spinaci, formare i ravioli, cuocere in acqua salata, condire con burro e salvia

## Bruschetta al Pomodoro
Ingredienti: pane casereccio, pomodori ciliegino, aglio, basilico, olio extravergine
Preparazione: tostare il pane, strofinare l'aglio, tagliare i pomodorini, condire con olio e basilico
=======
## Tiramisu'
Ingredienti: mascarpone, savoiardi, caffe, uova, cacao
Preparazione: montare tuorli con zucchero, aggiungere mascarpone, inzuppare savoiardi nel caffe, alternare strati, spolverare con cacao

## Panna Cotta
Ingredienti: panna, zucchero, colla di pesce, vaniglia
Preparazione: scaldare la panna con zucchero e vaniglia, aggiungere colla di pesce ammorbidita, versare negli stampini, raffreddare in frigo 4 ore
>>>>>>> origin/development
```

Cosa significano i marker:

| Marker | Significato |
|---|---|
| `<<<<<<< HEAD` | Inizio delle **tue** modifiche (la versione di Leonardo) |
| `=======` | Separatore — qui finiscono le tue modifiche |
| `>>>>>>> origin/development` | Fine delle **loro** modifiche (la versione di Simone, gia' in development) |

Leonardo risolve manualmente. La versione finale di `src/ricette.md` mantiene **tutte** le ricette:

```markdown
# Ricette

## Pizza Margherita
Ingredienti: farina, pomodoro, mozzarella, basilico

## Pasta Carbonara
Ingredienti: spaghetti, guanciale, uova, pecorino

## Ravioli di Ricotta e Spinaci
Ingredienti: farina, ricotta, spinaci, noce moscata, burro, salvia
Preparazione: impastare la farina con le uova, preparare il ripieno con ricotta e spinaci, formare i ravioli, cuocere in acqua salata, condire con burro e salvia

## Bruschetta al Pomodoro
Ingredienti: pane casereccio, pomodori ciliegino, aglio, basilico, olio extravergine
Preparazione: tostare il pane, strofinare l'aglio, tagliare i pomodorini, condire con olio e basilico

## Tiramisu'
Ingredienti: mascarpone, savoiardi, caffe, uova, cacao
Preparazione: montare tuorli con zucchero, aggiungere mascarpone, inzuppare savoiardi nel caffe, alternare strati, spolverare con cacao

## Panna Cotta
Ingredienti: panna, zucchero, colla di pesce, vaniglia
Preparazione: scaldare la panna con zucchero e vaniglia, aggiungere colla di pesce ammorbidita, versare negli stampini, raffreddare in frigo 4 ore
```

Leonardo ha rimosso tutti i marker (`<<<<<<<`, `=======`, `>>>>>>>`) e ha tenuto tutto il contenuto di entrambe le versioni. Ora registra la risoluzione:

```bash
git add src/ricette.md
git commit -m "merge: risolve conflitto ricette - mantiene tutte le ricette"
git push
```

Il `git add` dopo la risoluzione dice a Git: "Ho correttato il file, controlla che non ci siano piu' marker." Se restassero marker nel file, Git rifiuterebbe il commit.

---

## 6.7 La PR viene mergeata

Ora che il conflitto e' risolto, la PR puo' essere mergiata. Simone fa la review:

```bash
# Simone (reviewer)
gh pr review 3 --approve --body "Conflitto risolto correttamente. Tutte le ricette presenti."
```

Leonardo mergea:

```bash
# Leonardo
gh pr merge 3 --merge
```

GitHub mostra "Pull request successfully merged" — e questa volta senza sorprese, perche' il conflitto e' gia' stato risolto localmente.

---

## 6.8 Sincronizzazione finale

Entrambi gli sviluppatori aggiornano il loro `development` locale:

```bash
# Entrambi
cd ~/progetti/RistoranteAPI
git checkout development
git pull origin development
git log --oneline --graph -10
```

L'output mostra la storia completa con i due merge e il commit di risoluzione:

```
*   abc5678 (HEAD -> development) Merge pull request #3 from leonardo/feature/02-piatti-speciali
|\
| * def4567 merge: risolve conflitto ricette - mantiene tutte le ricette
| * cde3456 feat: aggiunge ricetta bruschetta e aggiorna ravioli
* | bcd2345 Merge pull request #2 from simone/feature/03-ricette-dolci
* | efg6789 feat: aggiunge ricette dolci (tiramisu e panna cotta)
|/
* aab1234 feat: aggiorna menu con nuove sezioni e piatti
```

Pulizia dei worktree — il lavoro e' finito:

```bash
# Leonardo
cd ~/progetti/RistoranteAPI
git worktree remove ../RistoranteAPI-piatti

# Simone
cd ~/progetti/RistoranteAPI
git worktree remove ../RistoranteAPI-ricette
```

Verifica che siano stati rimossi:

```bash
git worktree list
```

Output atteso — solo il worktree principale:

```
/home/simone/progetti/RistoranteAPI  abc5678 [development]
```

---

## 6.9 Cosa e' successo — dietro le quinte

Il conflitto e' avvenuto perche' **entrambi gli sviluppatori hanno modificato lo stesso file** in posizioni sovrapposte. Git ha provato a fare un merge automatico, ma non poteva decidere quali righe tenere.

Ecco cosa e' successo tecnicamente:

1. `git merge origin/development` ha portato i commit di `development` dentro la branch di Leonardo
2. Git ha trovato che `ricette.md` era stato modificato da entrambe le parti nella stessa zona
3. Invece di scegliere arbitrariamente, Git ha inserito i **conflict markers** — testo letterale nel file
4. I marker sono testo reale nel file: finche' restano, il file e' in uno stato non valido
5. La risoluzione consiste nell'editare il file, rimuovere i marker e scegliere il contenuto finale
6. `git add` segnala a Git che il conflitto e' risolto
7. `git commit` crea un **merge commit** con due genitori

### Come prevenire i conflitti

| Strategia | Come funziona |
|---|---|
| **Aggiornamenti frequenti** | Fai `git merge origin/development` spesso nella tua feature branch — cogli i conflitti presto |
| **PR piccole** | Meno file toccati = meno probabilita' di conflitto |
| **Comunicazione** | Se sapete che toccherete lo stesso file, coordinarvi prima |
| **Separazione dei file** | Quando possibile, lavorate su file diversi |

### I tre esiti di un merge

```
git merge origin/development
         │
         ├──► Fast-forward (nessun commit nuovo)
         │    La tua branch era indietro, nessuna divergenza
         │
         ├──► Merge automatico (nuovo merge commit)
         │    Git riesce a combinare le modifiche da solo
         │
          └──► Conflitto (richiede intervento manuale)
               Git non puo' decidere: modifica il file e committa
```

> **Nota GitLab:** I conflitti di merge sono gestiti da Git, non dalla piattaforma. `git merge`, i conflict markers, la risoluzione manuale — tutto e' identico. L'unica differenza e' come la piattaforma mostra il conflitto nell'interfaccia web prima del merge.

---

## Riepilogo comandi

| Comando | Scopo |
|---|---|
| `git pull origin development` | Aggiorna il branch locale con il remoto |
| `git worktree add <percorso> <branch>` | Crea un nuovo worktree |
| `git fetch origin` | Scarica i dati dal remoto senza mergiare |
| `git merge origin/development` | Porta le modifiche di development nel branch corrente |
| `git add <file>` | Segnala che il conflitto e' risolto |
| `git commit -m "merge: ..."` | Crea il merge commit dopo la risoluzione |
| `git push` | Invia la risoluzione al remoto |
| `gh pr create --base development --head <branch>` | Apre una Pull Request |
| `gh pr review <N> --approve --body "..."` | Approva una PR |
| `gh pr merge <N> --merge` | Mergea una PR |
| `git log --oneline --graph -10` | Mostra la storia con grafo dei merge |
| `git worktree remove <percorso>` | Rimuove un worktree |

---

*Prossima lezione: [Lezione 7 — Rebase: riscrivere la storia]*
