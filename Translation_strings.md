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
2. `<<<ricompilare it.po>>>`, ricompila il file di traduzione
3. `git add po/it.po` aggiunge il file di traduzione
4. `git commit -m 'Italian translation: recompiled it.po'`, ne fa il commit
5. `git push --set-upstream origin trad-ita-x.x.x`, e invia il branch al repo remoto, rendendolo disponibile a tutti 

### Traduzione
Una volta che il file di traduzione è pronto ci si deve spartire "spazialmente" le stringhe da tradurre. Il problema principale è che il file è soltanto uno, e se due membri del team fanno modifiche diverse allo stesso tempo sulla stessa stringa, questo generarà un conflitto da gestire manualmente. 

Quindi, assumendo che il file sia di 4500 stringhe e che il team sia composto da 3 persone, ciascuno avrà la responsabilità di operare su 1500 stringhe (da 1 a 1500, da 1501 a 3000 e da 3001 a 4500), evitando a prescindere conflitti sulle traduzioni. 

I conflitti possono comunque verificarsi, ed è bene ricordare che vanno gestiti con i `rebase` e non con i `merge`, e che bisogna evitare assolutamente il `git push --force`.

Quindi, prima di **ogni sessione** di traduzione, accertarsi che:
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
