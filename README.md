# Corso: Git & GitHub per Team — Il Progetto RistoranteAPI

**Durata:** ~3 ore | **Livello:** Base-intermedio | **Prerequisiti:** Conoscenza base di `git add`, `git commit`, `git push`

---

## Introduzione

Questo corso insegna a collaborare su un progetto GitHub usando **worktree**, **pull request**, **branch**, **risoluzione conflitti**, **rollback**, **hotfix** e **GitHub Pages con MkDocs**.

Il formato e pratico e basato su un **progetto simulato**: due persone — **Simone** e **Leonardo** — collaborano a un repository chiamato `RistoranteAPI`, lavorando su file diversi (e a volte sugli stessi), incontrando conflitti reali e risolvendoli insieme.

Ogni lezione corrisponde a un passaggio del flusso di lavoro reale. Potete seguire il corso in due (ruoli Simone e Leonardo) o da soli, simulando entrambi i ruoli con worktree separati. Simone e Leonardo lavorano su un **server Linux** usando **Antigravity** connesso tramite **Remote-SSH**.

---

## Indice

| # | Lezione | Argomento | Tempo |
|---|---------|-----------|-------|
| 0 | [Setup — Installazione e configurazione](lezione-00.md) | Git, GitHub, worktree, MkDocs | 15 min |
| 1 | [Simone crea il progetto](lezione-01.md) | Init, struttura repo, primo commit, branch `development` | 15 min |
| 2 | [Leonardo si unisce](lezione-02.md) | Clone, fork, permessi collaboratore, branch di feature | 10 min |
| 3 | [Simone — Worktree e prima feature](lezione-03.md) | `git worktree add`, branch `feature/menu`, commit atomici | 20 min |
| 4 | [Leonardo — Worktree parallelo](lezione-04.md) | Branch `feature/piatti`, lavoro parallelo senza conflitti | 20 min |
| 5 | [La prima Pull Request](lezione-05.md) | Creare PR, code review, merge su `development` | 15 min |
| 6 | [Conflitto di merge](lezione-06.md) | Conflitto su `src/ricette.md`, risoluzione manuale, merge | 25 min |
| 7 | [Rollback e correzioni](lezione-07.md) | `git revert`, `git reset`, tornare a versioni precedenti | 20 min |
| 8 | [Hotfix di produzione](lezione-08.md) | Branch `hotfix/*`, fix urgente su `main`, merge back | 15 min |
| 9 | [Documentazione e GitHub Pages](lezione-09.md) | MkDocs, `mkdocs.yml`, deploy su GitHub Pages | 25 min |

**Totale:** ~180 minuti (3 ore)

---

## Il progetto simulato

Il repository `RistoranteAPI` e un piccolo progetto markdown che rappresenta la documentazione di un ristorante. Quattro file, due collaboratori, un sacco di occasioni per imparare.

```
RistoranteAPI/
├── README.md              # Presentazione del progetto (condiviso)
├── src/
│   ├── menu.md            # Il menu del ristorante (Simone)
│   ├── piatti.md          # Descrizione dettagliata piatti (Leonardo)
│   └── ricette.md         # Ricette e preparazione (condiviso — fonte di conflitti)
└── docs/
    └── index.md           # Documentazione MkDocs (Lezione 9)
```

### Proprieta dei file

| File | Proprietario | Conflitti? | Perche |
|---|---|---|---|
| `README.md` | Condiviso | Raro | Modifiche poco frequenti |
| `src/menu.md` | Simone | No | Un solo contributore |
| `src/piatti.md` | Leonardo | No | Un solo contributore |
| `src/ricette.md` | Condiviso | **Si** | Entrambi modificano le stesse sezioni |

Il file `src/ricette.md` e il **campo di battaglia**: Simone e Leonardo lavorano entrambi sulle ricette, creando il conflitto perfetto per la Lezione 6.

---

## Branch Strategy

Il progetto usa un modello di branching semplice ma realistico:

```
main ────────────────────────────────────────── hotfix/corrisone-prezzo
 │                                              │
 │  development ────────────────────────────────│───── merge hotfix back
 │   │        │         │                       │
 │   │   feature/menu  feature/piatti    feature/ricette-simone
 │   │   (Simone)      (Leonardo)        (Simone)
 │   │
 │   └── feature/ricette-leonardo
 │        (Leonardo)
 │
 └── merge development → main (release)
```

### Regole

| Branch | Scopo | Chi ci lavora |
|---|---|---|
| `main` | Codice in produzione, sempre deployabile | Solo tramite merge da `development` o `hotfix/*` |
| `development` | Integrazione, testing collaborativo | Simone e Leonardo |
| `feature/*` | Una feature per branch, una persona per branch | Il proprietario della feature |
| `hotfix/*` | Fix urgenti da `main` | Chiunque, poi merge back |

### Flusso di lavoro

1. Creare un branch `feature/nome` da `development`
2. Lavorare nel worktree dedicato
3. Aprire una Pull Request verso `development`
4. Code review e merge
5. Periodicamente: merge `development` → `main`

> **Antigravity**: oltre ai comandi da terminale, potete usare il pannello **Source Control** nella sidebar sinistra (icona del branch) per lo staging, i commit, il branching e la gestione delle modifiche. Il terminale integrato e comunque lo strumento principale del corso.

---

## Personaggi

### Simone — Il project owner

- Crea il repository `RistoranteAPI`
- Gestisce il menu (`src/menu.md`)
- Configura GitHub Pages
- Responsabile dei merge su `main`
- Usa worktree per lavorare a piu feature in parallelo

### Leonardo — Il collaboratore

- Si unisce al progetto come collaboratore
- Gestisce la descrizione dei piatti (`src/piatti.md`)
- Contribuisce alle ricette (`src/ricette.md`)
- Impara il flusso PR e la risoluzione conflitti
- Usa worktree per il lavoro parallelo

---

## Requisiti

| Requisito | Dettagli |
|---|---|
| **Account GitHub** | Un account gratuito. Simone crea il repo, Leonardo viene aggiunto come collaboratore |
| **Piattaforma** | Server Linux accessibile via SSH. Simone e Leonardo usano Antigravity connesso tramite Remote-SSH |
| **Git** | Versione 2.17+ (per i worktree). Pre-installato sul server Linux |
| **Terminale** | Il terminale integrato di Antigravity (bash sul server Linux remoto) |
| **Editor di testo** | **Antigravity** — IDE derivato da VS Code, connesso al server Linux via Remote-SSH. Tutta la modifica dei file avviene nell'editor di Antigravity |
| **Python 3.8+** | Per MkDocs (Lezione 9). Verificare con `python3 --version` |
| **gh (GitHub CLI)** | Potrebbe non essere installato. Vedere Lezione 0 per l'installazione |

### Verifica rapida

```bash
git --version          # >= 2.17 (pre-installato sul server Linux)
python3 --version      # >= 3.8 (per MkDocs, Lezione 9)
gh --version           # GitHub CLI — se mancante, vedere Lezione 0
```

---

## GitHub vs GitLab — Differenze chiave

Le differenze tra GitHub e GitLab sono per lo meno terminologiche. I concetti di base (branch, merge, worktree, conflitti) sono identici perche' sono funzionalita' di Git, non della piattaforma. Questo corso usa GitHub come piattaforma di riferimento, ma alla fine di ogni lezione troverai una nota con le differenze specifiche per GitLab.

| Aspetto | GitHub | GitLab |
|---|---|---|
| Pull Request | Pull Request (PR) | Merge Request (MR) |
| CLI | `gh` (GitHub CLI) | `glab` (GitLab CLI) |
| CI/CD | GitHub Actions (`.github/workflows/*.yml`) | GitLab CI/CD (`.gitlab-ci.yml`) |
| Pages | GitHub Pages (Settings → Pages) | GitLab Pages (job `.gitlab-ci.yml` con `pages`) |
| Collaboratori | Settings → Collaborators | Settings → Members (con ruoli Guest/Reporter/Developer/Maintainer/Owner) |
| Branch protection | Settings → Branches → Branch protection rules | Settings → Repository → Protected branches |
| Code review | Review changes → Approve/Request changes | Approve button (con approval rules configurabili) |
| Merge options | Merge commit / Squash / Rebase | Merge commit / Squash / Fast-forward (piu' opzioni) |
| Default branch | main/master | main/master |
| Costo | Gratuito per repo pubblichi | Gratuito (self-hosted o gitlab.com) |
| Proprieta | Microsoft | Open source (edition community) |

---

*Corso creato per il progetto AthenaAI. Ogni lezione e autocontenuta e puo essere seguita indipendentemente, ma il percorso completo dalla Lezione 0 alla 9 offre l'esperienza piu completa.*
# corso-git-github
