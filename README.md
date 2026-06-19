# Grammatical Facial Expressions — Klasterovanje

Ovaj projekat se bavi analizom skupa podataka o gramatičkim izrazima lica primenom metoda nenadgledanog učenja, odnosno klasterovanja.

Cilj projekta je da se nad istim skupom podataka primeni više algoritama za klasterovanje, da se uporede njihovi rezultati i da se proveri koliko dobijeni klasteri odgovaraju stvarnim kategorijama izraza lica.

---

## 📊 Podaci

Skup podataka sadrži uzorke izraza lica podeljene u gramatičke kategorije.

Podaci su preuzeti sa UCI Machine Learning Repository:

1. **Izvor podataka:** [Grammatical Facial Expressions Data Set](https://archive.ics.uci.edu/dataset/317/grammatical+facial+expressions)
2. **Početni podaci:** originalni `.txt` fajlovi nalaze se u folderu `data/raw/`
3. **Preprocesirani podaci:** objedinjeni i obrađeni skup nalazi se u fajlu `data/preprocessed/preprocessed_dataset.csv`
4. **Redukovani podaci:** skalirani i PCA redukovani skupovi nalaze se u folderu `data/reduced/`

Kategorije izraza koje se koriste u projektu su:

- affirmative
- conditional
- doubt_question
- emphasis
- negative
- relative
- topics
- wh_question
- yn_question

U eksperimentima su korišćene dve verzije podataka:

1. **Scaled dataset** — skup podataka nakon skaliranja numeričkih atributa.
2. **PCA dataset** — redukovani skup podataka dobijen primenom PCA metode.

---

## 📁 Struktura projekta

```text
data/
├── raw/                 Početni podaci
├── preprocessed/        Preprocesirani podaci
└── reduced/             Skalirani i PCA redukovani podaci

src/
├── Preprocessing/       Notebook za preprocesiranje podataka
├── Reduction/           Notebook za PCA redukciju dimenzionalnosti
└── ClusteringAlgorithms/
    ├── KMeans/
    ├── DBSCAN/
    ├── hdbscan/
    ├── Agglomerative Hierarchial Clustering/
    ├── GMM/
    └── final_comparison.ipynb

results/
├── kmeans/
├── dbscan/
├── hdbscan/
├── ahc/
├── gmm/
└── final_comparison/
```

Napomena: naziv foldera `Agglomerative Hierarchial Clustering` je zadržan zbog postojeće strukture projekta, iako je ispravan naziv algoritma `Agglomerative Hierarchical Clustering`.

---

## 🧹 Preprocesiranje i redukcija dimenzionalnosti

U fazi preprocesiranja početni podaci se učitavaju, organizuju u jedinstvenu tabelarnu strukturu i pripremaju za primenu algoritama klasterovanja.

Rezultat preprocesiranja je fajl:

```text
data/preprocessed/preprocessed_dataset.csv
```

Nakon toga se primenjuju dodatne transformacije podataka. Generišu se:

```text
data/reduced/X_scaled.npy
data/reduced/X_pca.npy
```

`X_scaled.npy` predstavlja skalirani skup atributa, dok `X_pca.npy` predstavlja redukovani skup atributa dobijen PCA metodom.

Na ovaj način se ispunjava zahtev da se modeli primene i nad skupom sa svim obrađenim atributima i nad redukovanim skupom atributa.

---

## 🤖 Korišćeni algoritmi

U projektu je implementirano i evaluirano pet algoritama za klasterovanje:

1. **KMeans**
2. **DBSCAN**
3. **HDBSCAN**
4. **Agglomerative Hierarchical Clustering**
5. **Gaussian Mixture Models**

Svaki algoritam je primenjen nad oba skupa podataka:

- Scaled dataset
- PCA dataset

---

## 📏 Evaluacione metrike

Za evaluaciju rezultata korišćene su interne i eksterne metrike.

Interne metrike:

- **Silhouette score**
- **Davies-Bouldin score**

Eksterne metrike:

- **Adjusted Rand Index**
- **Normalized Mutual Information**

Dodatne metrike specifične za pojedine algoritme:

- **Noise ratio** za DBSCAN i HDBSCAN
- **Selection score** za DBSCAN i HDBSCAN
- **BIC/AIC** za GMM

ARI i NMI koriste stvarne oznake klasa i zato su korišćene samo za spoljašnju evaluaciju rezultata. Nisu korišćene kao glavni kriterijum za izbor parametara, jer algoritmi klasterovanja tokom rada nemaju pristup stvarnim klasama.

Kod DBSCAN i HDBSCAN algoritama, Silhouette i Davies-Bouldin metrike računaju se samo nad tačkama koje nisu označene kao šum, jer šum ne predstavlja regularan klaster.

---

## ⚙️ Izbor modela i parametara

Za svaki algoritam korišćen je odgovarajući postupak izbora parametara.

| Algoritam | Metod izbora |
|---|---|
| KMeans | Elbow metoda |
| DBSCAN | `Selection_score = Silhouette_non_noise - Noise_ratio` |
| HDBSCAN | `Selection_score = Silhouette_non_noise - Noise_ratio` |
| Agglomerative Hierarchical Clustering | Najbolji Silhouette score |
| GMM | Najbolja BIC vrednost |

Finalne konfiguracije modela nalaze se u fajlu:

```text
results/final_comparison/final_model_configurations.csv
```

---

## 📈 Rezultati

Rezultati pojedinačnih algoritama nalaze se u zasebnim folderima:

```text
results/kmeans/
results/dbscan/
results/hdbscan/
results/ahc/
results/gmm/
```

Finalno poređenje svih algoritama nalazi se u folderu:

```text
results/final_comparison/
```

Najvažniji fajlovi finalnog poređenja su:

```text
results/final_comparison/final_comparison_summary.csv
results/final_comparison/best_algorithm_by_dataset.csv
results/final_comparison/final_model_configurations.csv
```

Fajl `final_model_configurations.csv` sadrži finalne izabrane konfiguracije modela za svaki algoritam i oba skupa podataka.

Rezultati pokazuju da različiti algoritmi optimizuju različite aspekte problema. Neki algoritmi daju bolju unutrašnju strukturu klastera, dok drugi daju nešto bolje poklapanje sa stvarnim oznakama izraza.

Generalno, niske ARI i NMI vrednosti ukazuju na to da stvarne klase izraza nisu jasno razdvojene u korišćenom prostoru atributa.

---

## 🔁 Reprodukcija rezultata

Za pokretanje projekta lokalno potrebno je prvo instalirati zavisnosti:

```bash
pip install -r requirements.txt
```

Nakon toga notebooke treba pokretati sledećim redosledom:

```text
src/Preprocessing/preprocessing.ipynb
src/Reduction/PCA.ipynb
src/ClusteringAlgorithms/KMeans/kmeans.ipynb
src/ClusteringAlgorithms/DBSCAN/dbscan.ipynb
src/ClusteringAlgorithms/hdbscan/hdbscan.ipynb
src/ClusteringAlgorithms/Agglomerative Hierarchial Clustering/ahc.ipynb
src/ClusteringAlgorithms/GMM/gmm.ipynb
src/ClusteringAlgorithms/final_comparison.ipynb
```

Pokretanjem ovih notebooka ponovo se generišu preprocesirani podaci, redukovani skupovi podataka, rezultati klasterovanja, evaluacione tabele i finalno poređenje algoritama.

---

## 📦 Zavisnosti

Potrebne Python biblioteke navedene su u fajlu:

```text
requirements.txt
```

Glavne biblioteke koje se koriste su:

- numpy
- pandas
- matplotlib
- seaborn
- scikit-learn
- scipy
- hdbscan
- jupyter

---

## 📝 Napomene

Ovaj projekat se bavi klasterovanjem, a ne klasifikacijom.

Stvarne oznake izraza koriste se tek nakon klasterovanja, za evaluaciju pomoću ARI i NMI metrika. Algoritmi tokom klasterovanja ne koriste informaciju o pravim klasama.

Zato visok Silhouette score ne mora nužno da znači da se dobijeni klasteri dobro poklapaju sa stvarnim kategorijama izraza.

---

## ⚠️ Izazovi

Glavni izazov bio je to što kategorije izraza nisu jasno razdvojene u izabranom prostoru atributa. Zbog toga neki algoritmi pronalaze geometrijski smislene grupe, ali se te grupe ne poklapaju uvek dobro sa stvarnim oznakama izraza.

Dodatni izazov bilo je fer poređenje različitih tipova algoritama. DBSCAN i HDBSCAN mogu da označe deo tačaka kao šum, dok KMeans, AHC i GMM svaku tačku dodeljuju nekom klasteru. Zbog toga su za DBSCAN i HDBSCAN posebno prikazani noise ratio i selection score.