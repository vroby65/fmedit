# Guida alla manutenzione

Il branch `italiano` mantiene interfaccia, conferme, documentazione e test in italiano; `main` è in inglese. Applicare gli aggiornamenti funzionali a entrambi, conservando la lingua di ciascuno. Per modifiche specifiche alla versione italiana, commit e push vanno su `italiano`.

`fmedit` è un editor da terminale per Linux scritto in Bash 4+, senza compilazione
né dipendenze ncurses. Consultare `README.md` per comandi e limiti supportati.

- `fmedit`: unico sorgente ed eseguibile. `z_main` inizializza il terminale e avvia
  il ciclo lettura, dispatch e rendering; può essere caricato con `source` per
  verificare le funzioni senza avviare l'interfaccia.
- `tests/test_editor.py`: test del buffer, dei salvataggi e di sessioni su
  pseudo-terminale, con la sola libreria standard Python.
- Installazione utente: collegamento a `fmedit` in `~/.local/bin`, come nel README.

La palette ANSI `Z_*` riprende i colori di `fm` per editor, selezione, barre,
prompt e aiuto; ogni cambio di stile deve mantenere lo sfondo esplicito.

Le modifiche al buffer devono rispettare selezione e cronologia undo/redo.
La cronologia conserva intervalli sostituiti e inseriti, posizione della modifica
e cursore, non copie complete del buffer. `z_checkpoint` riceve inizio, fine e
testo inserito prima della sostituzione. `z_index` mantiene `buffer_lines` e
`buffer_offsets` in caratteri, ricostruiti quando `text` cambia; `z_location`
cerca la riga nell’indice e il rendering accede solo alle righe visibili.
Una riga vuota finale rappresenta il newline finale del buffer.
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

For manual verification, open a temporary file with `./fmedit`, edit, save with F2
and quit with Ctrl+Q. Update the README and this guide when commands, dependencies,
structure, invariants or verification procedures change. Run relevant checks and
review the diff before finishing.
