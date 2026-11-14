# Guida alla manutenzione

`fmedit` è un editor da terminale per Linux scritto in Bash 4+, senza compilazione
né dipendenze ncurses. Consultare `README.md` per comandi e limiti supportati.

- `fmedit`: unico sorgente ed eseguibile. `z_main` inizializza il terminale e avvia
  il ciclo lettura, dispatch e rendering; può essere caricato con `source` per
  verificare le funzioni senza avviare l'interfaccia.
- `tests/test_editor.py`: test del buffer, dei salvataggi e di sessioni su
  pseudo-terminale, con la sola libreria standard Python.
- Installazione utente: collegamento a `fmedit` in `~/.local/bin`, come nel README.

Le modifiche al buffer devono rispettare selezione e cronologia undo/redo.
Preservare esattamente i byte dei file supportati e rifiutare i NUL. Il salvataggio
usa un temporaneo nella destinazione, mantiene i permessi e segue i symlink.
Il rendering non deve eseguire sequenze di controllo provenienti dai file.
Ogni uscita deve ripristinare lo stato del terminale. Le funzioni richiamate dai
trap devono considerare lo scope dinamico delle variabili Bash, incluso `IFS`.

Verifica dalla radice del progetto:

```bash
bash -n fmedit
python3 -m unittest discover -s tests -v
./fmedit --help
./fmedit --version
git diff --check
```

Per una verifica manuale, aprire un file temporaneo con `./fmedit`, modificare,
salvare con F2 e uscire con Ctrl+Q. Aggiornare README e questa guida quando cambiano
comandi, dipendenze, struttura, invarianti o procedure di verifica. Prima di
concludere, eseguire i controlli pertinenti e rivedere il diff.
