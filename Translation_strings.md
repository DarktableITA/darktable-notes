# Appunti di traduzione di Darktable
I seguenti appunti sono stati concordati con il team della documentazione di dt

## Collaborare con git nella traduzione
Tradurre in team può essere complicato, data la struttura del file di traduzione `po.it`. 
Sostanzialmente il problema si pone quando due o più membri del team modificano contemporanamente la stessa stringa, causando un conflitto e conseguente perdita di tempo.

Per girare attorno al problema è sufficiente un minimo di organizzazione.

Si consiglia comunque di utilizzare [Lokalize](https://apps.kde.org/en-gb/lokalize/) per la traduzione

### Preparazione
Nel momento dello _string freeze_ si allinea il fork `darktableITA` con il master branch su `darktable`, si crea un nuovo branch per la traduzione `trad-ita-x.x.x` con la target release e si ricompila il file di traduzione.

Quindi, da terminale con la working directory nella git root di `darktableITA`:

1. `git checkout -b trad-ita-x.x.x`, git crea il branch e ci si porta automaticamente
2. ricompila il file di traduzione
3. `git add po/it.po` aggiunge il file di traduzione
4. `git commit -m 'Italian translation: recompiled it.po'`, ne fa il commit
5. `git push --set-upstream origin trad-ita-x.x.x`, e invia il branch al repo remoto, rendendolo disponibile a tutti 

### Traduzione
Una volta che il file di traduzione è pronto ci si deve spartire "spazialmente" le stringhe da tradurre. Il problema principale è che il file è soltanto uno, e se due membri del team fanno modifiche diverse allo stesso tempo sulla stessa stringa, questo generarà un conflitto da gestire manualmente. 

Quindi, assumendo che il file sia di 4500 stringhe e che il team sia composto da 3 persone, ciascuno avrà la responsabilità di operare su 1500 stringhe (da 1 a 1500, da 1501 a 3000 e da 3001 a 4500), evitando a prescindere conflitti sulle traduzioni. 

I conflitti possono comunque verificarsi, ed è bene ricordare che vanno gestiti con i `rebase` e non con i `merge`, e che bisogna evitare assolutamente il `git push --force`.

Quindi, **prima di ogni sessione** di traduzione, accertarsi che:

- si sia nel branch corretto (`git switch trad-ita-x.x.x`)
- non ci siano modifiche locali (con `git status`)
- che si abbia l'ultima versione disponibile (con `git pull`)

Quindi si comincia a tradurre e quando la sessione è finita si consiglia di non attendere oltre per committare. 

```sh
git commit -am 'Italian translation' 
git push`
```

Se il push viene rifiutato significa che qualcun'altro ha fatto un commit prima di te, portando "avanti" la git history del branch. Ad esempio:

```sh
To github.com:mmardegan/darktableITA.git
 ! [rejected]            trad-ita-x.x.x -> trad-ita-x.x.x (fetch first)
error: failed to push some refs to 'github.com:mmardegan/darktableITA.git'
```

In questo caso si procede ad un rebase, ovvero si dice a git di sincronizzare il branch locale con quello remoto e di riposizionare il proprio commit locale sopra ("on top") l'ultimo commit del branch remoto. L'unico conflitto che potrebbe avverarsi è sui metadati all'inizio del file su chi ha editato l'ultima volta il file. 

```sh
git fetch                           # Scarica i nuovi commit dal branch remoto
git rebase origin/trad-ita-x.x.x    # Effettua il rebase del commit locale
git push                            # Trasferisce il commit locale sul branch remoto
```

### Finalizzare le traduzioni
Nel momento in cui tutte le stringhe sono state tradotte e verificate, è finalmente possibile procedere alla pull request verso l'upstream (il master originale di darktable).  
Lavorando con le modalità sopra descritte succede, però, che la git history venga "inquinata" da i molti commit creati da tutti i membri del team: di per sé non rappresenta un problema ma aggiunge ben poco valore alla storia globale del progetto. Se tutti i commit sono sequenziali si può fare un rebase interattivo e accorpare tutti i commit (ma anche se non sono sequenziali, è solo più complicato).

In pratica, si istruisce git a **riscrivere** la git history locale, eliminando tutti i commit selezionati e creando un unico commit alternativo con tutte le modifiche. Alla fine dell'operazione la copia locale del branch divergerà da quella remota in maniera incompatibile, e sarà necessaria una forzatura.

Quindi, per fare l'accorpamento dei commit:

Innanzitutto, accertarsi di essere nel giusto branch, e che sia aggiornato (`git status`, `git pull`)

Successivamente, guardare al log per individuare il primo commit della sessione corrente di traduzione. Se si usa l'opzione `--online` è più semplice:

```sh
luca@l6:~/dev/darktableITA$ git log --oneline
6933fbe3cf (HEAD -> trad-ita-4.8-bis, origin/trad-ita-4.8-bis) Sostituito click/clic; corretto errori ortografici; allineato tasti Ctrl/Maiusc/Tab/Esc/Canc
642fc913c7 Sostituito click/clic; corretto errori ortografici; allineato tasti Ctrl/Maiusc/Tab/Esc/Canc
...
a9d66e7ce6 Updated italian translations
b58a152f5b Updated the it.po with darktable.pot catalog and traslated few strings
edbab2e4dc (origin/master, origin/HEAD) l10n: simplified chinese
d2b15e6524 scopes: fix outdated help URL
...
```

In questo caso l'`HEAD` è il commit corrent mentre il primo commit utile per la traduzione è `b58a152f5b`, ma conviene prendere come riferimento quello precedente.

Lanciare quindi il rebase interattivo chiedendo a git di coinvolgere tutti commit tra l'`HEAD` e il nostro commit di riferimento con `git rebase -i edbab2e4dc`. Il comando genera più o meno il testo seguente:

```
pick b58a152f5b Updated the it. po with darktable.pot catalog and traslated few strings
pick a9d66e7ce6 Updated italian translations
pick b969e79564 Updated italian translations 
pick 5ea0d4cf86 Updated italian translations 
pick 7695a6a782 Reviewed italian traslations 
pick 6e8989b510 Reviewed italian traslations 
pick 815575460a Reviewed italian traslations
pick 0f1f99ce78 **WIP** It. Translation: RESTORED upper case letters (90%)
pick f0552991e7 Reviewed italian traslations
pick b9a903b0e5 Reviewed italian traslations
pick 642fc913c7 Sostituito click/clic; corretto errori ortografici; allineato tasti Ctrl/Maiusc/Tab/Esc/Canc 
pick 6933fbe3cf Sostituito click/clic; corretto errori ortografici; allineato tasti Ctrl/Maiusc/Tab/Esc/Canc

# Rebase edbab2e4dc..6933fbe3cf onto edbab2e4dc (12 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit>= use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit>= use commit, but meld into previous commit
# ...

# ...
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
``` 

La lista dei commit è in ordine inverso, ed è possibile operare un'azione diversa per ciascun commit, per il nostro esercizio dobbiamo solo pensare a `pick` e `squash`.  
Il primo commit va tenuto (quindi `pick`), tutti gli altri vanno accorpati a quello precedente (quindi `squash`) dal basso verso l'alto:

```
pick b58a152f5b Updated the it. po with darktable.pot catalog and traslated few strings
squash a9d66e7ce6 Updated italian translations
squash b969e79564 Updated italian translations 
squash 5ea0d4cf86 Updated italian translations 
squash 7695a6a782 Reviewed italian traslations 
squash 6e8989b510 Reviewed italian traslations 
squash 815575460a Reviewed italian traslations
squash 0f1f99ce78 **WIP** It. Translation: RESTORED upper case letters (90%)
squash f0552991e7 Reviewed italian traslations
squash b9a903b0e5 Reviewed italian traslations
squash 642fc913c7 Sostituito click/clic; corretto errori ortografici; allineato tasti Ctrl/Maiusc/Tab/Esc/Canc 
squash 6933fbe3cf Sostituito click/clic; corretto errori ortografici; allineato tasti Ctrl/Maiusc/Tab/Esc/Canc
```

Nel momento in cui si confermano le azioni sui vari commit (salvando e uscendo da `vim` nel mio caso) git genera per il nuovo commit un commento unico multiriga, concatenando i commenti dei commit accorpati. In questo caso possiamo semplicemente ignorare (ed eliminare) tutti i commenti (generalmente saranno "traduzione italiana" "italian translation" "WIP italian translation" o similari) e scrivere un unico commento pulito: `update it.po`. 
Al salvataggio avremo:

```sh
luca@l6:~/dev/darktableITA$ git rebase -i edbab2e4dc
[detached HEAD bcb2ac94ee] update it.po
 Author: luca <cigno5@users.noreply.github.com>
 Date: Sun Jun 2 21:04:18 2024 +0200
 1 file changed, 5841 insertions(+), 4967 deletions(-)
Successfully rebased and updated refs/heads/trad-ita-4.8-bis.
```

Controllando con `git log --oneline` vedremo la git history completamente riscritta:

```sh
luca@l6:~/dev/darktableITA$ git log --oneline
bcb2ac94ee (HEAD -> trad-ita-4.8-bis) update it.po
edbab2e4dc (origin/master, origin/HEAD, master) l10n: simplified chinese
d2b15e6524 scopes: fix outdated help URL
683ff9faed Update nl.po
cb5ec6239c Update es.po
...
```

Ma `git status` evidenzierà lo stato divergente del branch locale:
```sh
luca@l6:~/dev/darktableITA$ git status 
On branch trad-ita-4.8-bis
Your branch and 'origin/trad-ita-4.8-bis' have diverged,
and have 1 and 12 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

nothing to commit, working tree clean
```

Ci comunica che il branch locale ha 1 commit che non esiste sul branch remoto, e che quest'ultimo ne ha 12 che non esistono sul branch locale. 
Come in un episodio di Ritorno al Futuro le due linee temporali si sono biforcate, ma, siccome sappiamo che quella locale è quella corretta, possiamo imporla su quella remota.

Prima di procedere vogliamo però essere sicuri che il commit sommario produca lo stesso risultato dei 12 che abbiamo accorpato. Dato che il branch remoto non è stato ancora modificato possiamo fare un `diff` tra l'head locale e remota:

```sh
luca@l6:~/dev/darktableITA$ git diff origin/trad-ita-4.8-bis 
luca@l6:~/dev/darktableITA$
```
Il diff è vuoto, quindi i due branch sono identici in termini di contenuto. Possiamo quindi riscrivere anche il branch remoto:

```sh
git push --force
```

**NOTA BENE**: il `git push --force` è una pratica che dovrebbe essere usata con molta attenzione, in quanto riscrive il branch remoto e rende automaticamente **divergenti tutti i branch locali dei memebri del team**, con conseguenze disastrose di _merge_ incontrollati. Una volta che si è eseguito il push force bisogna sempre avvertire i propri colleghi. 

## Dizionario
- adjust = regolazione
- aspect ratio = proporzioni
- backlight = controluce
- bias = compensazione
- border = bordo
- brightness = luminosità (brillanza)
- brilliance = brillantezza
- cancel = annulla
- chroma = cromaticità (croma)
- collection = raccolta
- color checker = scheda colori
- color picker = selettore colore
- colorfulness = vivacità colore
- crop factor = fattore di taglio / fattore di ritaglio
- cursor = selettore scorrevole (per comandi)
- cursor = cursore (per mouse)
- display (v) = visualizza
- display-referred = riferito allo schermo
- edge = margine
- feather = sfumatura
- flip = capovolgi
- gain = guadagno
- highlights = alte luci
- hue = tonalità (tinta)
- lightness = chiarezza
- liquify = fluidifica
- look up table = tabella di ricerca
- luminance = luminanza
- luma = luma
- midtones = mezzi toni
- offset = compensazione, spostamento
- scene-referred = riferito alla scena
- show = mostra
- slope = calo
- success = riuscito
- succesfully = correttamente
- threshold = soglia
- timeline = linea temporale
- toggle (toggle tool) = interruttore
- tone = tono
- watermark = filigrana
- zoom = ingrandire/ingrandimento

Il riferimento tecnico cui far ricorso per i termini che rientrano nella scienza della [colorimetria](https://it.wikipedia.org/wiki/Colorimetria) è 
il vocabolario pubblicato dall'[IEC "International Electrotechnical Commission"](http://www.iec.ch/), accessibile tramite questo link [Electropedia](https://www.electropedia.org/), 
la **categoria di riferimento è la n. 845	Lighting**. 

## Convenzioni 
### Maiuscole / Minuscole
I testi di Darktable, versione inglese, sono tutti in minuscolo, comprese sigle, abbreviazioni, ecc. Per la traduzione italiana si è discusso quale fosse 
per la lingua italiana la modalità più corretta e si è convenuto di adottare la maiuscola
ad inizio frase, per le sigle, per le abbreviazioni: nei sotto elenchi dovrà esser fatta attenzione a mantenere la minuscola. 
La questione è stata discussa in mailing list anche con il team di sviluppo di dt, che ha lasciato libera scelta.

### Scorciatoie da tastiera e mouse
Anche queste sono concordate con il team della documentazione di dt.

Il nome dei tasti deve iniziare con la lettera maiuscola.

Occorre utilizzare il segno + (per indicare che si tratta di una combinazione di tasti) senza spazi.

Il tasto Shift deve essere tradotto in Maiusc

Esempi:
- Ctrl+M
- Alt+V
- Maiusc+T

Stesse convenzioni per il mouse con una variante per quanto riguarda il tasto destro e la rotella)

Esempi:
- Ctrl+click
- Ctrl+(click destro)
- Ctrl+(rotella mouse)
