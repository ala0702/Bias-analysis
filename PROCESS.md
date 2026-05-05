# Process documentation

model used in web chat format: Gemini 3Flash

## Selecting project and dataset

prompt: "Projekt dotyczący biased data - jakie zbiory są dostepne i ktory najlepiej wybrac i dlaczgeo?"
with response:
"Projekt dotyczący **biased data** (stronniczych danych) to fascynujący temat, bo pozwala nie tylko na analizę techniczną, ale i etyczną. Wybór odpowiedniego zbioru zależy od tego, jaki rodzaj stronniczości chcesz zbadać: czy interesuje Cię dyskryminacja w zatrudnieniu, błędy w rozpoznawaniu twarzy, czy może uprzedzenia w modelach językowych.

            Oto zestawienie najciekawszych zbiorów oraz rekomendacja, który z nich będzie najlepszy na start.

            ### 1. Przegląd dostępnych zbiorów danych (Biased Datasets)

            | Nazwa zbioru      | Dziedzina               | Rodzaj biasu         | Dlaczego warto?                                                                                                         |
            | :---------------- | :---------------------- | :------------------- | :---------------------------------------------------------------------------------------------------------------------- |
            | **COMPAS**        | Prawo / Recydywa        | Rasowy               | Klasyk. Służy do przewidywania recydywy; wykazano, że system częściej mylił się na niekorzyść osób czarnoskórych.       |
            | **Adult Income**  | Finanse / Socjoekonomia | Płeć                 | Dane z cenzusu USA. Przewidywanie dochodów (>50k$), gdzie płeć i pochodzenie silnie korelują z wynikiem.                |
            | **German Credit** | Bankowość               | Wiek / Stan cywilny  | Mały, ale treściwy zbiór o przyznawaniu kredytów. Pozwala badać dyskryminację osób starszych lub o konkretnym statusie. |
            | **Gender Shades** | Computer Vision         | Płeć / Kolor skóry   | Zbiór stworzony przez Joy Buolamwini, pokazujący błędy AI w rozpoznawaniu twarzy kobiet o ciemniejszej karnacji.        |
            | **Toxic Comment** | NLP                     | Językowy / Kulturowy | Dane z Wikipedii/Jigsaw. Pozwala badać, czy algorytmy częściej flagują komentarze mniejszości jako "toksyczne".         |
            | **MBBMD (2026)**  | Media / Newsy           | Polityczny           | Nowoczesny zbiór (Media Bias Bias-Mitigated) do wykrywania stronniczości w artykułach prasowych.                        |

            ---

            ### 2. Rekomendacja: Co wybrać?

            Jeśli to Twój pierwszy taki projekt, najlepiej wybrać **Adult Income Dataset** (znany też jako Census Income) lub **COMPAS**.

            #### Dlaczego Adult Income to najlepszy wybór?

            1.  **Dostępność i dokumentacja:** Jest dostępny w repozytorium UCI Machine Learning oraz na Kaggle. Istnieją setki tutoriali, co ułatwia naukę.
            2.  **Jasne metryki:** Łatwo na nim policzyć tzw. _Disparate Impact_ (nieproporcjonalny wpływ) – np. sprawdzić, o ile rzadziej model "przyznaje" wysokie zarobki kobietom przy takich samych kwalifikacjach jak u mężczyzn.
            3.  **Wielowymiarowość:** Możesz badać bias nie tylko ze względu na płeć, ale też rasę, wiek czy kraj pochodzenia.

            ---

            ### 3. Jak podejść do analizy?

            Wybierając zbiór, Twój projekt powinien przejść przez następujące etapy:

            - **Eksploracja (EDA):** Wizualizacja rozkładów. Czy w danych jest 10 razy więcej mężczyzn niż kobiet? To pierwszy sygnał _representation bias_.
            - **Trenowanie modelu:** Stwórz "naiwny" model bez poprawek.
            - **Pomiar stronniczości:** Użyj bibliotek takich jak **AIF360** (IBM) lub **Fairlearn** (Microsoft). Zmierz _Equalized Odds_ lub _Demographic Parity_.
            - **Mitigacja:** Spróbuj "naprawić" model, np. przez re-weighting (nadanie większej wagi niedoreprezentowanym grupom) lub usuwanie korelacji między cechami wrażliwymi a wynikiem.

            ### Podsumowanie

            Wybierz **Adult Income**, jeśli chcesz skupić się na algorytmach klasycznych (tabele, statystyka). Wybierz **Toxic Comment Classification**, jeśli wolisz pracować z tekstem i nowoczesnymi modelami NLP (Transformerami), gdzie bias jest subtelniejszy i trudniejszy do wyeliminowania.

            Który z tych obszarów – dane tabelaryczne czy tekst – bardziej Cię interesuje w kontekście Twojego projektu?"

--> Adult dataset was chosen.

## Analysis of dataset

Later on we moved to load dataset into our notebook and go through the files with description.
Part of document:
"
note that we have class imbalance:
Class probabilities for adult.all file
| Probability for the label '>50K' : 23.93% / 24.78% (without unknowns)
| Probability for the label '<=50K' : 76.07% / 75.22% (without unknowns)

according to adult.names file and part:
"| Conversion of original data as follows:
| 1. Discretized agrossincome into two ranges with threshold 50,000.
| 2. Convert U.S. to US to avoid periods.
| 3. Convert Unknown to "?"
| 4. Run MLC++ GenCVFiles to generate data,test."

baseline accuracy:
Algorithm Error
| -- ---------------- -----
| 1 C4.5 15.54
| 2 C4.5-auto 14.46
| 3 C4.5 rules 14.94
| 4 Voted ID3 (0.6) 15.64
| 5 Voted ID3 (0.8) 16.47
| 6 T2 16.84
| 7 1R 19.54
| 8 NBTree 14.10
| 9 CN2 16.00
| 10 HOODG 14.82
| 11 FSS Naive Bayes 14.05
| 12 IDTM (Decision table) 14.46
| 13 Naive-Bayes 16.12
| 14 Nearest-neighbor (1) 21.42
| 15 Nearest-neighbor (3) 20.35
| 16 OC1 15.04
| 17 Pebls Crashed. Unknown why (bounds WERE increased)

columns:
age: continuous.
workclass: Private, Self-emp-not-inc, Self-emp-inc, Federal-gov, Local-gov, State-gov, Without-pay, Never-worked.
fnlwgt: continuous.
education: Bachelors, Some-college, 11th, HS-grad, Prof-school, Assoc-acdm, Assoc-voc, 9th, 7th-8th, 12th, Masters, 1st-4th, 10th, Doctorate, 5th-6th, Preschool.
education-num: continuous.
marital-status: Married-civ-spouse, Divorced, Never-married, Separated, Widowed, Married-spouse-absent, Married-AF-spouse.
occupation: Tech-support, Craft-repair, Other-service, Sales, Exec-managerial, Prof-specialty, Handlers-cleaners, Machine-op-inspct, Adm-clerical, Farming-fishing, Transport-moving, Priv-house-serv, Protective-serv, Armed-Forces.
relationship: Wife, Own-child, Husband, Not-in-family, Other-relative, Unmarried.
race: White, Asian-Pac-Islander, Amer-Indian-Eskimo, Other, Black.
sex: Female, Male.
capital-gain: continuous.
capital-loss: continuous.
hours-per-week: continuous.
native-country: United-States, Cambodia, England, Puerto-Rico, Canada, Germany, Outlying-US(Guam-USVI-etc), India, Japan, Greece, South, China, Cuba, Iran, Honduras, Philippines, Italy, Poland, Jamaica, Vietnam, Mexico, Portugal, Ireland, France, Dominican-Republic, Laos, Ecuador, Taiwan, Haiti, Columbia, Hungary, Guatemala, Nicaragua, Scotland, Thailand, Yugoslavia, El-Salvador, Trinadad&Tobago, Peru, Hong, Holand-Netherlands.
"

Based on this we selected columns and had already splitted dataset to train and test!

Next was preparation of dataset that contained changing dtypes.
Data Structure Optimization (Memory Downcasting)
Problem: Default loading of raw data in Pandas allocated "heavy" data types (e.g., int64 for simple numbers like age and str/object for text). This resulted in unnecessary RAM consumption and could potentially slow down the ML pipeline.
Solution: A downcasting process was implemented to reduce the memory footprint:
Converted text variables to the category type, which optimizes memory storage for repetitive strings (e.g., "Private" in the workclass column).
Mapped the target variable (income) from text labels to int8 (0 and 1).
Applied pd.to_numeric(downcast='integer') to numerical columns (e.g., converting age from int64 to int8, as values do not exceed 127).

### Formatting the Target Variable

- **Problem:** The target variable income logically represents binary values (true/false for earnings >50K). A dilemma arose regarding whether to use a bool or a numerical type.

- Decision: It was decided to use the int8 type (values 0 and 1) instead of bool with Gemini suggestion.

Justification: Using 0/1 is an industry standard that ensures broader compatibility. While scikit-learn supports boolean types, using int8 future-proofs the pipeline (e.g., allowing for an easy swap to models like XGBoost or LightGBM, which natively expect a numerical target). At the same time, it maintains the same minimal memory footprint (1 byte per row).

Fairness shouldnt be checked on final model but we should start to measure it and look for some pattern right from the start in EDA, as well to build understanding around dataset we will work with.

There can be diiffrent couses of model being unfair -- we will take specific look at it:))

## Starting EDA

this was kind of chain of thoughts process and had many iterations, first of all i started to do classic EDA that had many plots and i didnt ask myself what question do i want to answer.
later on i had clearer vision and used Gemini to help me in iterative way to correct my plots.
my initial prompt:
"sns.countplot(x='income', data=df) jak to polepszyc??? dataset Adult (predicting income >50K) i already changed to 0 and 1
. i want to be coheseve, value redeability and good practices"

different prompt:
"ax = sns.countplot(x='race', data=df, hue='race', legend=False, palette='Set2')
plt.title('Distribution of race', fontsize=16, pad=30)
plt.xlabel('Race', fontsize=12, labelpad=10)
plt.ylabel('Number of Individuals', fontsize=12, labelpad=10)
total = len(df['race'])
for p in ax.patches:
    percentage = f'{100 \* p.get_height() / total:.1f}%'
    x = p.get_x() + p.get_width() / 2 - 0.05
    y = p.get_height()
    ax.annotate(f'{int(p.get_height())}\n({percentage})', (x, y), ha='center', fontsize=11)

sns.despine()
plt.show()

and this? how to make it proper?"

On the way i had problem with labels not displaying labels in the same line as bar line.
"podpisy ras nie sa na wysokosci slupkow...
napisy nie sa ucinane ale np white nie jest na tej samym poziomie co slupek poziomy"

In answer i got solution as to set parameter dodge=False:))

next i wanted to update colors
prompt: "moge to zmienic tak zeby najczesciej wystepujaca rasa byla kolorem a reszta slupkow na szaro?"
And it helped to do so:)

ALso my code have been updated to contain number values over bars.

Then i wasnt sure to contain some specific plots with proxy variables, I got response as
"Jeśli chcesz utrzymać prostotę (Cohesive view): Skup się tylko na relationship jako proxy dla płci. native-country możesz pominąć, bo dominacja USA (90%) sprawia, że ta zmienna wnosi niewiele do ogólnego obrazu, a komplikuje wykresy."
But still decided to do it for "race" as well

Chat Gemini was used to put nicely conclusions and as well to explain most important metrics.

At the begfgining i was having only one function per one case but after couple of iterations the approach was to have one function to get way cleaner code and more coheseve.

example promt for interpretation of data: "Selection Rate to 26% dla mężczyzn vs 8% dla kobiet -- Czy to nie wynika po prostu z tego że mamy mniej kobiet w danych?"

# Key decisions in project were to:

- in final version stic t to random forest ( at the beggining we were as well showing results of logistic regression but it was way to much information to gasp and results were worst compared to decision forest)
- decide for baseline, unawarness nad drop proxy approach
- comparison in subgroups to get more data insgiht
- Fairlearn as library
- selecting pre-processinf as solution (deteing proxy-variables and fairness through unawarness)

for future i would recommend In-processing method that was not evaluated in this project
