# Domanda

Io ho un repository con un progetto di un blog Hugo che sincronizzo con github. La folder public del progetto blog hugo vorrei pubblicarla su codeberg, come faccio a fare il push solo della folder public su codeberg?

# Risposta
Sì, puoi farlo senza problemi. Se vuoi che **solo `public/`** finisca in un repository Codeberg, mentre il progetto Hugo completo continua ad avere GitHub come remote, hai sostanzialmente due possibilità.

Per il tuo caso ti consiglio **un secondo repository Git dentro `public/`**. È semplice e mantiene completamente separati i due repository.

### 1. Vai nella cartella `public`

Dal progetto Hugo:

```bash
cd public
```

### 2. Inizializza un repository Git lì dentro

```bash
git init
```

### 3. Aggiungi il repository Codeberg come remote

Ad esempio, se su Codeberg hai creato:

```text
https://codeberg.org/tuonome/mio-blog.git
```

fai:

```bash
git remote add origin https://codeberg.org/tuonome/mio-blog.git
```

oppure, se vuoi usare SSH:

```bash
git remote add origin git@codeberg.org:tuonome/mio-blog.git
```

### 4. Genera il sito Hugo

Dalla directory principale del progetto:

```bash
hugo
```

Questo aggiornerà `public/`.

Poi:

```bash
cd public
git add .
git commit -m "Update blog"
git push -u origin main
```

Se il repository Codeberg è vuoto e il tuo branch locale è `master`, puoi invece fare:

```bash
git branch -M main
git push -u origin main
```

---

### Ma c'è una cosa importante

Il tuo repository principale sarà strutturato così:

```text
blog/
├── content/
├── layouts/
├── static/
├── config...
├── public/          ← secondo repository Git
│   ├── index.html
│   ├── posts/
│   ├── css/
│   └── ...
└── .git/
```

Il `.git` dentro `public/` **non viene gestito dal repository Git principale**: `public` diventa un repository indipendente.

Quindi avrai:

```text
GitHub
└── repository Hugo
    ├── content
    ├── layouts
    └── public/  ← ignorata dal Git principale

Codeberg
└── repository del sito
    ├── index.html
    ├── posts
    └── ...
```

Nel `.gitignore` del progetto Hugo ti conviene quindi avere:

```gitignore
/public/
```

In questo modo il repository GitHub contiene il **sorgente Hugo**, mentre Codeberg contiene esclusivamente il **sito HTML generato**.

### E per aggiornare tutto

Ogni volta che modifichi il blog:

```bash
# dal progetto Hugo
hugo

# poi
cd public
git add .
git commit -m "Update blog"
git push
```

È probabilmente la soluzione più pulita se il tuo obiettivo è avere **GitHub per il codice/sorgenti Hugo e Codeberg per il sito generato**.

Se invece vuoi evitare di dover fare `cd public` e i tre comandi Git ogni volta, si può anche creare uno **script di deploy di 3-4 righe** che fa `hugo` e poi automaticamente il push su Codeberg.
