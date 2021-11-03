# Tecnologie del Linguaggio Naturale

###### Francesco Sannicola, Matteo Parisi

## Prima-seconda esercitazione

#### Introduzione

La prima consegna riguarda il calcolo della consistenza tra definizioni effettuate da noi studenti. Il task svolto a lezione prevedeva la selezione di 4 concetti, tra tutti quelli proposti dagli studenti, e di descrivere tali parole mediante definizioni. Ogni persona ha effettuato l'annotazione in modo indipendente cioè in base alla sua conoscenza del concetto.

Sono stati scelti 4 concetti di cui 2 generici e 2 specifici. Ogni concetto  generici o specifici può essere a sua volta astratto o concreto:

- **courage** 
- **paper**
- **apprehension**
- **sharpener**

Il file *definizioni.csv* contiene 30 definizioni per ogni concetto.

#### Sviluppo

- ***remove_stopwords(words_list)***: prende in input la frase ed elimina tutti le sstopwords contenute nel file *stop_words_FULL.txt* 

- ***remove_punctuation(sentence)***: data una frase, sarà restituita una frase senza punteggiatura.

- **tokenize_sentence(sentence)**: viene effettuato lo splitting della frase sfruttando gli spazi e, successivamente, ogni parola viene portata al suo lemma.

- ***get_signature(sense)***: attraverso l'uso dei metodi precedentemente descritti e delle API di Wordnet viene calcolata la **signature**, ovvero la concatenazione delle parole pre-processate delle definizioni e i termini provenienti dagli esempi in WN per un determinato senso.

- ***get_definitions()*** è la funzione che va a leggere il file *definizioni.csv* e restituisce un dizionario dove la chiave è rappresentata dal termine e il valore da una lista di **BoW** correlata alle definizioni. Vengono utilizzati i metodi menzionati in precedenza.

- ***cosine_sim(def1, def2)*** : prende in input due insiemi di parole corrispondenti a due definizioni distinte, crea due vettori numerici, uno per *def1* e uno per *def2*, che conterranno 1 se la parola in analisi appartiene al set e 0 altrimenti. Ciò viene fatto per ogni parola che appare nelle due definizioni. Il calcolo della similarità del coseno viene effuata proprio tra questi due vettori numerici. La similarità è uguale al rapporto del prodotto scalare tra i vettori e il prodotto delle loro norme.
  $$
  cosine(v_1,v_2) = \frac{v_1 \bullet v_2}{||v_1|| \cdot ||v_2||}
  $$

- ***compute_result(definitions_words)***: prende in input il dizionario di liste contenente le definizioni processate e calcola la similarità tra tutte le coppie di definizioni dello stesso concetto. Restituirà il valore medio delle similrità calcolate per ogni concetto.

- ***most_frequent_words(definitions)***: metodo che va a calcolare le parole più frequenti all'interno delle definizioni di un concetto. In particolare, per ogni concetto, calcola quelle parole che appaiono almeno nel 50% delle definizioni.

#### Risultati

I risultati ottenuti sono in linea con quanto ci aspettavamo: i concetti più astratti sono descritti mediante delle definizioni poco simili. Al contrario i concetti concreti, *paper* e *sharpener*, vengono annotate attraverso definizioni più semplici e in linea tra di loro.

Nel dettaglio:

| Concetto | Similarità definizioni |
| :------------------: | :---------: |
|       Courage       |   0.2105   |
|     Paper |   0.2925   |
| Apprehension |  0.0830  |
| Sharpener |   0.3863   |

I risultati ottenuti dipendono principalmente dalla presenza di parole ripetute nelle varie definizioni. Il metodo *most_frequent_words()* ci fa notare la presenza di un maggior numero di parole ripetute tra le definizioni del concetto *Sharpener*.  *Courage* e *Paper* condividono il numero di parole ripetute tra definizioni.
| Concetto | Parole ripetute |
| :------------------: | :---------: |
|       Courage       |   ability, fear   |
|     Paper |   write, material   |
| Apprehension |    |
| Sharpener |   sharpen, pencil, tool   |



## Terza esercitazione

#### Introduzione

Tra le tre proposte è stato scelto il task di **caratterizzazione delle definizione in Wordnet**. L'obiettivo è lo studio e la ricerca di vari pattern all'interno dell'ontologia di Wordnet. Questi pattern fanno riferimento alle definizioni, in particolare della loro lunghezza, e alle relazioni tipiche di Wordnet (iperonimia e iponimia).

Gli studi effettuati sono 4:

1. Calcolo della lunghezza media delle definizioni appartenenti ad ogni categoria presente in Wordnet, ovvero nomi, verbi, aggettivi e avverbi.
2. Calcolo delle lunghezze delle definizioni di tutti gli iperonimi di un determinato concetto.
3. Calcolo distanza del concetto in analisi dal nodo radice e confronto con le distanze ottenute tra le parole più significative della definizione della parola in analisi e il nodo root più vicino.
4. Calcolo overlap tra la definizione del concetto in analisi e le definizioni di iperonimi ed iponimi. Viene fatta una media aritmetica degli score di similarità degli iperonimi e degli iponimi in modo da ottenere due valori da poter confrontare. Inizialmente era stato deciso di utilizzare gli score Rogue e Bleu per questo tipo di task. Purtroppo, queste due metriche non sono adatte al calcolo della similarità (overlap) tra due frasi bensì per valutare riassunti e traduzioni automatiche. La versione definitiva prevede l'utilizzo di un modello basato sulle *reti transformer*: il testo sarà prima mappato in uno spazio vettoriale e poi sarà effettuata la similarità del coseno direttamente nel nuovo spazio ([Link Github](https://github.com/UKPLab/sentence-transformers)).

#### Sviluppo

- **remove_stopwords(words_list)**, **remove_punctuation(sentence)**, **tokenize_sentence(sentence)**, **get_signature(sense)**: implementazione analoga ad esercizio precedente.
- **avg_len_section_definitons()**: implementazione primo studio. Viene calcolata la lunghezza media di ogni definizione per sezione. 
- **all_hypernym_paths(word)**: implementazione secondo studio. Data una parola viene considerato ogni suo significato e per ogni suo significato vengono calcolati i suoi iperonimi. Per ogni iperonimo fino al nodo radice vengono calcolate le lunghezze delle definizioni.
- **distance_root(word)** e **calculate_distance_root(synset)**: implementazione terzo studio. Richiede in input la parola e viene restituito un dizionario di dizionari contenente come chiavi i vari significati del concetto in analisi e come valore le parole associate alla definizione del concetto con la relativa distanza (minima) dalla radice.
- **definition_overlap(word)**: implementazione ultimo studio. Viene calcolato uno score che indica quanto le definizioni di iperonimi e iponimi sono simili alla definizione del concetto in analisi. Sono state utilizzate il module *sentence_transformers* per il calcolo dei vettori embedded corrispondenti alle varie definizioni e la libreria *scipy.spatial* utilizzata per il calcolo della distanza del coseno. I vettori embedded vengono computati a partire da un modello basato sulla rete transformer BERT pre-addestrato sulla lingua inglese e ottimizzato per vari task. I valori restituiti saranno due per ogni synset del concetto in analisi: essi corrispondono al valore medio di somiglianza tra le definizioni di tutti gli iperonimi/iponimi e la definizione della parola in analisi.

#### Risultati

- **Primo Studio**

  | Categoria | Lunghezza media definizioni |
  | :-------: | :-------------------------: |
  |   Nomi    |            11,47            |
  |   Verbi   |            6,14             |
  | Aggettivi |            7,23             |
  |  Avverbi  |            5,02             |

  Notiamo che i nomi vengono descritti utilizzando un quantitativo maggiore di parole. A seguire i verbi, aggettivi e avverbi.

- **Secondo studio**

  A seguire il risultato ottenuto per un solo senso e per ogni parola in analisi.

  <code> 

  ```python
  Concept:  Courage
  [(Synset('entity.n.01'), 17), (Synset('abstraction.n.06'), 11), (Synset('attribute.n.02'), 9), (Synset('trait.n.01'), 7), (Synset('character.n.03'), 18), (Synset('spirit.n.03'), 9), (Synset('courage.n.01'), 15)] # a partire dal synset 'courage.n.01'
  
  Concept:  Paper
  [(Synset('entity.n.01'), 17), (Synset('physical_entity.n.01'), 6), (Synset('matter.n.03'), 7), (Synset('substance.n.01'), 11), (Synset('material.n.01'), 12), (Synset('paper.n.01'), 15)] # a partire dal synset 'paper.n.01', ci sono altri 8 sensi per Paper
  
  Concept:  Apprehension
  [(Synset('entity.n.01'), 17), (Synset('abstraction.n.06'), 11), (Synset('attribute.n.02'), 9), (Synset('state.n.02'), 10), (Synset('feeling.n.01'), 7), (Synset('emotion.n.01'), 3), (Synset('fear.n.01'), 20), (Synset('apprehension.n.01'), 4)] # a partire dal synset 'apprehension.n.01', ci sono altri 3 sensi per Apprehension
  
  Concept:  Sharpener
  [(Synset('entity.n.01'), 17), (Synset('physical_entity.n.01'), 6), (Synset('object.n.01'), 12), (Synset('whole.n.02'), 11), (Synset('artifact.n.01'), 7), (Synset('instrumentality.n.03'), 13), (Synset('implement.n.01'), 12), (Synset('sharpener.n.01'), 14)] # a partire dal synset 'sharpener.n.01'
  ```

  </code>
  
- **Terzo studio**

  Viene stampato per ogni senso del concetto in analisi, la distanza minima tra le parole più signiificative della definizione e il nodo radice.

  <code> 

  ```python
  Concept:  Courage
  {Synset('courage.n.01'): {'Courage': 7, 'quality': 1, 'spirit': 5, 'enable': 2, 'face': 1, 'danger': 4, 'pain': 4, 'fear': 1}}
  # Courage : 7 vuol dire che il Synset('courage.n.01') ha distanza dal più vicino nodo radice pari a 7.
  
  Concept:  Paper
  {Synset('paper.n.01'): {'Paper': 6, 'material': 1, 'cellulose': 9, 'pulp': 3, 'derive': 1, 'wood': 6, 'rag': 2, 'grass': 2}, ...
   
   Concept:  Apprehension
  {Synset('apprehension.n.01'): {'Apprehension': 8, 'fearful': 1, 'expectation': 6, 'anticipation': 7}, ...
   
   Concept:  Sharpener
  {Synset('sharpener.n.01'): {'Sharpener': 8, 'implement': 2, 'edge': 2, 'point': 2, 'sharper': 1}}
  ```
  
  </code> 
  
- **Quarto studio**
  
  Anche in questo caso consideriamo i risultati ottenuti su un singolo synset associato alla parola in analisi. Per una completa visione della soluzione rimandiamo al notebook.
  
  | Concetto     |    Synset considerato    | Similarità media iperonimi | Similarità media iponimi |
  | :------------: | :----------------------: | :------------------: | :------------------: |
  | Courage      |  Synset('courage.n.01')  |        0.5595        | 0.6555            |
  | Paper        |   Synset('paper.n.01')   |         0.4182         | 0.544 |
  | Apprehension | Synset('apprehension.n.01') |           0.7708           | 0.8047 |
  | Sharpener    | Synset('sharpener.n.01') |         0.733         | 0.6331 |
  
  Tre risposte su quattro mostrano che le definizioni degli iponimi siano mediamente più vicine alla definizione del concetto in analisi. Questo vale anche per i sensi non mostrati nella tabella precedente. Importante sottolineare che la metà dei sensi, in questo caso di studio, non ha iponimi e, quindi, non è stato possibile calcolare un valore di similarità.
  
  
  

## Quarta esercitazione

#### Introduzione

L'obiettivo dell'esercitazione è lo studio di alcuni verbi dal punto di vista della **Teoria di Hanks**. Il verbo è la radice del significato. Non esistono espressioni senza verbo. Ad ogni verbo viene associata una valenza che indica il numero di argomenti necessari per il verbo. In base al numero di argomenti che un verbo richiede, in alcuni casi possiamo differenziarne il significato. Una volta determinato il numero di argomenti di un verbo dobbiamo specificarli mediante un certo numero di slot. Ogni slot può avere un certo numero di valori che lo riempiono, detti **filler**. Ogni filler può avere associati dei **tipi semantici** che rappresentano delle generalizzazioni concettuali strutturate come una gerarchia. Raggruppiamo i vari filler secondo alcuni types. Essi sono **gruppi semantici** generici o categorizzazioni/clusterizzazioni dei filler.

E' stato scelto di effettuare gli esperimenti su due verbi in particolare:

1. **eat**
2. **buy** 

Inoltre sono state considerate solo le forme del verbo al presente, quindi: eat, eats, buy, buys. 

I corpus sono stati estratti da *Sketch Engine* tramite il tool *Concordance*. Essi contengono circa 10000 frasi in inglese composte dal verbo in questione. Ogni frase descrive un contesto generico. 

#### Sviluppo

- **remove_stopwords(words_list)**, **remove_punctuation(sentence)**, **tokenize_sentence(sentence)**, **get_signature(sense)**: implementazione analoga ad esercizio precedente.

- **search_obj(sentence, head_verb, pattern)**: data una frase elaborata come oggetto *spacy*, il verbo e un numero minore di 3 di argomenti all'interno del pattern, viene calcolato l'oggetto della frase in base alla sua costruzione sintattica a dipendenze. 

  Un esempio di pattern: 

  <code>

  ```python
  [('You', 'PRP', 'buy', 'nsubj'), ('products', 'NNS', 'buy', 'dobj'), 
  You can buy our products by PAYPAL Or Credit Card. 
    ]
  ```

  </code>

- **search_subj(sentence, head_verb, pattern)**: funzione analoga a quella precedente, ma per il calcolo del soggetto.

- **disambiguate_terms(pattern)** : una volta ottenuti i pattern all'interno del corpus si effettua la disambiguazione ovvero viene associato un senso di Wordnet alle dipendenze di ogni pattern, attraverso l'algoritmo **Lesk**.

- **semantic_clusters(patterns)**:  dato in input una lista di pattern vengono calcolati i cluster semantici. Viene utilizzata la funzione precedentemente descritta per l'assegnazione del senso alle due dipendenze. Attraverso l'attributo *lexname* otteniamo il supersenso associato ai due argomenti.

  Il calcolo dei cluster viene effettuato mediante l'ausilio del modulo *Counter* il quale va a creare un dizionario le cui chiavi corrispondono alle coppie *supersense1, supersense2* e il valore al numero di volte che appare quella coppia all'interno della lista di pattern. Una volta fatto ciò è semplice attribuire una percentuale di appartenenza ai cluster calcolati implicitamente con *Counter*. Infine viene restituita una struttura ordinata in base a tale percentuale.

- **find_patterns()**: in seguito all'inizializzazione di *spacy* e al parsing del file XML del corpus tramite *minidom*, vengono invocate le funzioni per il calcolo dell'oggetto e del soggetto di ogni frase. Vengono salvati esclusivamente i pattern contenenti esattamente due argomenti.

#### Risultati

Per entrambi i corpus (eat e buy) sono stati trovati circa 200 cluster semantici.

Nelle due prossime tabelle verranno mostrati i 10 cluster più frequenti per ogni verbo in analisi.

- **Buy**

| Cluster                                 | Percentuale di appartenenza |
| --------------------------------------- | --------------------------- |
| ('noun.quantity', 'noun.cognition')     | 7.48%                       |
| ('noun.group', 'noun.artifact')         | 4.61%                       |
| ('noun.quantity', 'noun.communication') | 3.59%                       |
| ('noun.person', 'noun.artifact')        | 2.66%                       |
| ('noun.act', 'noun.substance')          | 2.25%                       |
| ('noun.group', 'noun.communication')    | 2.25%                       |
| ('noun.substance', 'noun.artifact')     | 1.98%                       |
| ('noun.quantity', 'noun.artifact')      | 1.95%                       |
| ('noun.quantity', 'noun.person')        | 1.84%                       |
| ('noun.quantity', 'noun.act')           | 1.84%                       |

- **Eat** 

| Cluster                             | Percentuale di appartenenza |
| ----------------------------------- | --------------------------- |
| ('noun.quantity', 'noun.food')      | 8.86%                       |
| ('noun.quantity', 'noun.cognition') | 5.35%                       |
| ('noun.group', 'noun.food')         | 5.00%                       |
| ('noun.person', 'noun.food')        | 4.12%                       |
| ('noun.quantity', 'noun.artifact')  | 2.81%                       |
| ('noun.substance', 'noun.food')     | 2.63%                       |
| ('noun.quantity', 'noun.quantity')  | 2.19%                       |
| ('noun.group', 'noun.cognition')    | 1.84%                       |
| ('noun.person', 'noun.cognition')   | 1.75%                       |
| ('noun.group', 'noun.act')          | 1.49%                       |

