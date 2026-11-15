# fmedit

Questa è la versione italiana, mantenuta nel branch `italiano`. La versione inglese è nel [branch `main`](https://github.com/vroby65/fmedit/tree/main). Per scegliere questa versione in un clone, eseguire `git switch italiano`.

Un piccolo editor da terminale scritto in Bash, ispirato al progetto Zepto
presente in questa macchina. Un solo script, senza compilazione e senza ncurses.
È pensato per i file di testo e gli script di shell.

`main` contains the English version. The [italiano branch](https://github.com/vroby65/fmedit/tree/italiano)
contains the Italian version. To use it, run `git switch italiano` in a clone.

## Getting started

```bash
./fmedit notes.txt
./fmedit                 # new unnamed buffer
./fmedit -- -file-name    # name beginning with a dash
```

Requires Bash 4+, an ANSI/VT terminal, and `stty`, `mktemp`, `cp`, `mv`, `rm`.
Saving through a symbolic link also requires `readlink -f`. Python is not needed
at runtime. Developed and tested on Linux.

To add it to your PATH, run from the project directory:

```bash
mkdir -p ~/.local/bin
ln -s "$PWD/fmedit" ~/.local/bin/fmedit
```

The symlink follows the branch checked out in this directory.

## Commands

| Key | Action |
| --- | --- |
| F1 | Aiuto |
| F2 / Ctrl+S | Salva; chiede il nome per un nuovo buffer |
| F10 | Salva ed esci |
| Esc / Ctrl+Q | Esci; con modifiche chiede se salvare, scartare o annullare |
| Frecce, Home, End, PgUp, PgDown | Sposta il cursore |
| Ctrl+Home / Ctrl+End | Inizio / fine documento |
| Shift+frecce, Shift+Home/End | Seleziona testo |
| Ctrl+A | Seleziona tutto |
| Ctrl+C / Ctrl+X / Ctrl+V | Copia / taglia / incolla negli appunti interni |
| Ctrl+Z / Ctrl+Y | Annulla / ripeti (ultime 100 modifiche) |
| F7 / Ctrl+F | Cerca testo letterale, distinguendo maiuscole e minuscole |
| Ctrl+N | Cerca l'occorrenza successiva, ricominciando dall'inizio |

Nei prompt si scrive la risposta e si preme Invio; Esc annulla.
Il collegamento di installazione segue il branch attualmente selezionato nel clone.
Il terminale deve inoltrare le combinazioni all'editor: alcune applicazioni
intercettano i tasti funzione o Shift+frecce.

## Funzionamento

- Palette coordinata con `fm`: sfondo grigio scuro, testo giallo tenue e
  selezione petrolio; barre, prompt e aiuto usano gli stessi colori di `fm`.
- Numeri di riga, scorrimento verticale e orizzontale, indicatore delle modifiche.
  Un indice delle righe evita di ripercorrere il documento a ogni spostamento;
  il disegno legge direttamente le righe della pagina visibile.
- Undo/redo conserva solo il testo sostituito e inserito, fino a 100 modifiche,
  anziché copiare l’intero documento per ogni carattere digitato.
- Colori essenziali per commenti, stringhe e `$` nei file `.sh` e `.bashrc`;
  l'analisi è limitata alla singola riga.
- Incolla dal terminale tramite bracketed paste, registrato come una modifica.
  I tasti di controllo incollati non vengono eseguiti come comandi dell'editor.
- UTF-8 secondo la locale di Bash. Tab visualizzati a intervalli di quattro colonne.
- Conserva esattamente il testo letto, compresa la presenza o assenza dell'ultimo
  newline e i terminatori CRLF. I file contenenti NUL vengono rifiutati.
- Salva tramite file temporaneo nella cartella di destinazione e rinomina finale;
  preserva i permessi dei file esistenti. I file nuovi hanno permessi `0600`.
  Segue i collegamenti simbolici esistenti senza sostituire il collegamento.
- Ripristina modalità del terminale, cursore e schermo all'uscita e sui segnali
  HUP, INT e TERM. Le modifiche non salvate restano solo in memoria.

## Limiti attuali

Il buffer resta una stringa Bash e l’indice viene ricostruito dopo le modifiche:
file molto grandi e righe molto lunghe possono ancora risultare lenti. Le
selezioni estese conservate nella cronologia possono consumare molta memoria.
Ogni carattere digitato è una voce di cronologia. Emoji, caratteri a doppia
larghezza e segni combinanti possono disallineare il cursore. I caratteri di controllo nei file sono
mostrati come `?`; non vengono inviati direttamente al terminale.

Non sono ancora presenti mouse, buffer multipli, sostituzione del testo o
integrazione con gli appunti di sistema. Il salvataggio con rinomina sostituisce
l'inode: eventuali hard link continuano a puntare alla vecchia versione. Non
gestisce modifiche simultanee da altri programmi né garantisce la persistenza
in caso di interruzione dell'alimentazione.

## Verifica

I test richiedono Python 3 della distribuzione e usano solo la libreria standard:

```bash
bash -n fmedit
python3 -m unittest discover -s tests -v
```

Tests cover buffer operations, file preservation and real pseudo-terminal sessions
for shortcuts, paste, prompts and terminal restoration. They also check navigation
and editing across 40,000 lines with external commands disabled, saving 10,000
UTF-8/CRLF lines, English help, overwrite confirmation and save/discard on exit.
