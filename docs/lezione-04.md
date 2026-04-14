# Lezione 4 — Leonardo: worktree parallelo

**Obiettivo:** Lavorare in parallelo con worktree separati

**Tempo stimato:** 20 minuti

---

## 4.1 Leonardo aggiorna il suo repo locale

Leonardo deve vedere il nuovo branch che Simone ha pubblicato nella lezione precedente. Il suo repository locale non sa ancora che esiste.

```bash
# Leonardo
cd ~/progetti/RistoranteAPI
git fetch --all
```

`git fetch --all` scarica tutti i nuovi dati dal remote — branch, commit, tag — senza modificare la working directory. A differenza di `git pull`, non esegue un merge automatico.

Verifica i branch remoti disponibili:

```bash
git branch -r
```

Output atteso:

```
origin/development
origin/main
origin/feature/01-menu-giorno
```

Ora Leonardo vede `origin/feature/01-menu-giorno`, il branch creato da Simone.

Si posiziona su `development` e lo aggiorna:

```bash
git checkout development
git pull origin development
```

La sua copia locale di `development` e' ora sincronizzata con il remote.

---

## 4.2 Leonardo crea il suo worktree

Leonardo crea un worktree per la sua feature — i piatti speciali:

```bash
git worktree add ../RistoranteAPI-piatti feature/02-piatti-speciali
```

Questo comando crea la directory `../RistoranteAPI-piatti` e fa il checkout del branch `feature/02-piatti-speciali` al suo interno.

Si sposta nel nuovo worktree:

```bash
cd ~/progetti/RistoranteAPI-piatti
git branch
```

Output:

```
* feature/02-piatti-speciali
```

Leonardo e' ora su `feature/02-piatti-speciali` in una directory isolata. Questo branch e' stato creato a partire da `development` — il branch su cui si trovava quando ha eseguito `git worktree add`.

---

## 4.3 Leonardo lavora sui piatti

Leonardo modifica `src/piatti.md` — aggiunge piatti e crea la sezione speciali:

In Antigravity: apri `src/piatti.md` e sostituisci il contenuto con:

```markdown
# Catalogo Piatti

## Antipasti
* Bruschetta al Pomodoro
* Caprese con Bufala
* Carpaccio di Manzo

## Primi
* Carbonara Tradizionale
* Amatriciana
* Cacio e Pepe
* Risotto ai Funghi

## Secondi
* Cotoletta alla Milanese
* Pesce Spada alla Griglia
* Ossobuco alla Milanese

## Dolci
* Tiramisu'
* Panna Cotta
* Cannolo Siciliano

## Piatti Speciali dello Chef
* Ravioli di Ricotta e Spinaci
* Filetto al Pepe Verde
* Souffle' al Cioccolato
```

Poi modifica `src/ricette.md` — aggiunge una nuova ricetta:

In Antigravity: apri `src/ricette.md` e sostituisci il contenuto con:

```markdown
# Ricette

## Pizza Margherita
Ingredienti: farina, pomodoro, mozzarella, basilico

## Pasta Carbonara
Ingredienti: spaghetti, guanciale, uova, pecorino

## Ravioli di Ricotta e Spinaci
Ingredienti: farina, ricotta, spinaci, noce moscata, burro, salvia
Preparazione: impastare la farina con le uova, preparare il ripieno con ricotta e spinaci, formare i ravioli, cuocere in acqua salata, condire con burro e salvia
```

---

## 4.4 Commit e push

Leonardo salva e pubblica il suo lavoro:

```bash
git add src/piatti.md src/ricette.md
git commit -m "feat: aggiorna catalogo piatti con sezione speciali e ricetta ravioli"
git push -u origin feature/02-piatti-speciali
```

Ora il branch `feature/02-piatti-speciali` esiste sul remote di GitHub, indipendente dal branch di Simone.

---

## 4.5 Verifica del parallelismo

Leonardo verifica lo stato dei suoi worktree:

```bash
git worktree list
```

Output atteso:

```
/home/leonardo/progetti/RistoranteAPI           abc1234 [development]
/home/leonardo/progetti/RistoranteAPI-piatti    def5678 [feature/02-piatti-speciali]
```

Torna al repository principale e verifica che `development` sia intatto:

```bash
cd ~/progetti/RistoranteAPI
git log --oneline
```

L'output mostra solo i commit originali — nessuna traccia del lavoro sui piatti.

L'insight chiave: **il worktree e' completamente isolato da development**. Leonardo e Simone stanno lavorando contemporaneamente su due feature diverse, ognuno nel proprio worktree, senza interferenze.

---

## 4.6 Il concetto di lavoro parallelo

La situazione attuale del repository:

```
development ──────────────────────────────────
     │                                         
     ├── feature/01-menu-giorno (Simone) ──────●
     │                                         
     └── feature/02-piatti-speciali (Leonardo) ─●
```

Entrambi i branch sono nati dallo stesso punto su `development`. Ogni sviluppatore:

- Lavora nella propria directory isolata
- Committa sul proprio branch senza toccare `development`
- Non blocca il lavoro dell'altro

I branch saranno riuniti tramite Pull Request nella prossima lezione.

---

## 4.7 Cosa e' successo — dietro le quinte

Leonardo visualizza l'albero dei branch:

```bash
git log --oneline --graph --all
```

L'output mostra due linee divergenti da `development`:

- `feature/01-menu-giorno` con il commit di Simone su `menu.md`
- `feature/02-piatti-speciali` con il commit di Leonardo su `piatti.md` e `ricette.md`

Entrambi i branch condividono lo stesso commit ancestor su `development`.

**Nessun conflitto — per ora.** Simone ha modificato `menu.md`, Leonardo ha modificato `piatti.md` e `ricette.md`. File diversi, nessuna sovrapposizione.

Ma Leonardo **ha toccato `ricette.md`** — e questo avra' importanza nella lezione 6, quando i branch verranno mergati e qualcun altro avra' modificato lo stesso file.

> **Nota GitLab:** Il lavoro parallelo con worktree e' identico su GitLab. Tutto cio' che riguarda Git (worktree, branch, commit, push) funziona esattamente allo stesso modo. Le differenze emergono solo quando si usano le funzionalita' della piattaforma (PR/MR, CI/CD, Pages).

---

## Riepilogo comandi

| Comando | Scopo |
|---|---|
| `git fetch --all` | Scarica tutti i dati dal remote senza modificare i file |
| `git branch -r` | Elenca i branch remoti |
| `git checkout development` | Passa al branch development |
| `git pull origin development` | Aggiorna il branch locale con il remote |
| `git worktree add <percorso> <branch>` | Crea un nuovo worktree |
| `cd ~/progetti/RistoranteAPI-piatti` | Si sposta nel worktree |
| `git branch` | Verifica il branch corrente |
| `git add <file1> <file2>` | Stage di piu' file |
| `git commit -m "messaggio"` | Commit con messaggio |
| `git push -u origin <branch>` | Pubblica il branch e setta upstream |
| `git worktree list` | Elenca tutti i worktree |
| `git log --oneline` | Mostra la cronologia compatta |
| `git log --oneline --graph --all` | Mostra albero di tutti i branch |

---

*Prossima lezione: Simone apre una Pull Request per la sua feature.*
