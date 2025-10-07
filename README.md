#**Analisi dati del sito e-commerce Olist store con Power BI**

**Obiettivo dell’analisi**

Sviluppare un report di BI che consenta di analizzare:
1.	L’andamento degli ordini nel tempo per stato
2.	L’andamento dei ricavi nel tempo per stato
3.	Distribuzione del rating

**Fonti dei dati**

Sono stati forniti 9 file in formato .csv di cui 5 sono risultati utili ai fini delle analisi. Di seguito sono elencati e descritti i dataset utilizzati e le loro caratteristiche principali:

DATASET | DESCRIZIONE | CAMPI

olist_orders_dataset | Informazioni sugli ordini | customer_id, customer_unique_id, customer_zip_code_prefix, customer_city, customer_state
olist_order_items_dataset | Dettagli sugli articoli ordinati | order_id, order_item_id, product_id, seller_id, shipping_limit_date, price, freight_value
olist_products_dataset | Caratteristiche dei prodotti |	product_id, product_category_name, product_weight_g, product_length_cm, product_height_cm, product_width_cm
olist_order_reviews_dataset | Recensioni e valutazioni dei clienti | review_id, order_id, review_score, review_creation_date, review_answer_timestamp
olist_customers_dataset | Dati anagrafici e geografici dei clienti | customer_id, customer_unique_id, customer_zip_code_prefix, customer_city, customer_state

**Trasformazione e pulizia dei dati**

Dopo aver caricato i dataset tramite la funzione Recupera dati, i dati sono stati trasformati in Power Query per garantire coerenza e qualità:

- Controllo e correzione dei tipi di colonna, assegnando a ciascun campo il tipo più appropriato (testo, data, numerico, valuta);
- Gestione e sostituzione di valori nulli o anomali dei campi utili all’analisi;
- Rimozione delle colonne non necessarie per ridurre il volume complessivo del dataset.

**Struttura del modello dati**

Una volta importate le tabelle su Power BI, sono state create le tabelle DAX Calendario e ReviewCalendar, ed in seguito impostato le seguenti relazioni nella sezione Model View creando uno Star Shema al cui centro si trova la tabella olist_orders_dataset:

- Relazione 1 a molti tra olist_orders_dataset e olist_order_items_dataset;
- Relazione 1 a 1 tra olist_orders_dataset e olist_customers_dataset;
- Relazione 1 a molti tra olist_orders_dataset e olist_order_reviews_dataset;
- Relazione molti a 1 tra olist_orders_dataset e Calendario;
- Relazione molti a 1 tra olist_order_items_dataset e olist_products_dataset;
- Relazione molti a 1 tra olist_order_reviews_dataset e ReviewCalendar.

**Misure DAX**

Al fine di rispondere alle richieste oggetto dell’analisi sono state create le seguenti Misure:

- ConteggioOrdini = COUNTROWS(olist_order_items_dataset)
- N.OrdiniAnnoCorrente = COUNT(olist_order_items_dataset[order_id])
- N.OrdiniAnnoPrecedente = CALCULATE(COUNT(olist_order_items_dataset[order_id]), PARALLELPERIOD(Calendario[Date],-12, MONTH))
- Delta%Ordini = DIVIDE([N.OrdiniAnnoCorrente]-[N.OrdiniAnnoPrecedente],[N.OrdiniAnnoPrecedente]);
- RicavoTotale = SUMX(olist_order_items_dataset, olist_order_items_dataset[price]+olist_order_items_dataset[freight_value])
- RicavoAnnoPrecedente = CALCULATE([RicavoTotale], PARALLELPERIOD(Calendario[Date],-12, MONTH))
- Delta%Ricavo = DIVIDE([RicavoTotale]-[RicavoAnnoPrecedente],[RicavoAnnoPrecedente])
- %Valutazioni = DIVIDE(COUNTROWS(olist_order_reviews_dataset),CALCULATE(COUNTROWS(olist_order_reviews_dataset),ALL(olist_order_reviews_dataset)))
- MediaValutazioni = AVERAGE(olist_order_reviews_dataset[review_score])


**Scelta cromatica e layout**

Per garantire coerenza visiva con l’identità del sito Olist Store, i colori utilizzati nei grafici e negli elementi del report riprendono la palette originale del brand.

**Scelte di storytelling**

Il report adotta uno storytelling descrittivo, focalizzato sulla presentazione chiara dei dati e sulla spiegazione dei grafici.
Non sono state incluse considerazioni conclusive o suggerimenti operativi, in quanto l’obiettivo del progetto è fornire una lettura oggettiva e neutrale dei risultati, coerente con le richieste della traccia d’esame.
Ho omesso raccomandazioni o interpretazioni avanzate anche perché molti risultati erano già evidenti dai dati e rischiavano di sovraccaricare la lettura piuttosto che aggiungere valore, mantenendo così il report focalizzato e leggibile.

