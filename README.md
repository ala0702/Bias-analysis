ANALYSIS FAIRNESS IN MACHINE LEARNING - MITIGATING BIAS IN MACHINE LEARNING

# Goal of project

In this project we will mainly focus on analysing if model is fair for specific groups (according to sex and race). Later on we will try to mitigate bias, dropping crucial and proxy columns.

# Dataset

For analysis we are usinf dataset Adult - tabular dataeset from 1994. The dateaset aims for prediction if invidual is going to exceed 50K/year
https://archive.ics.uci.edu/dataset/2/adult

# Pipeline

1. Baseline model (random forest) - complete dataset with all columns
2. 'fairness through unawarness" - trial of removing bias by dropping sensitive columns: "sex" and "race".
3. Proxy removal Strategy - Identyfying and deletion of mediating variables

# Metrics of fairness

- Demographic Parity Difference - DP
  DP demands that the model says "Yes (>50K)" to the exact same percentage of Men and Women, regardless of their actual qualifications

- Equalized Odds Difference - EO
  It measures whether the model makes the same amount of mistakes for both groups.
  It looks at two specific error rates:
  -> True Positive Rate (TPR):
  Out of all the people who actually deserve the >50K, how many did the model correctly identify?
  -> False Positive Rate (FPR)
  Out of all the people who don't deserve it, how many did the model mistakenly give it to?
  The Goal: The model must have the exact same TPR and FPR for Men and Women. If the model recognizes 80% of qualified Men, it must recognize 80% of qualified Women. EO Difference measures the gap between these rates (closer to 0 is better).

# Conclusions

### The baseline model (85.1% accuracy) showed deep systemic bias:

- Gender Bias: The model is 3x more likely to make mistakes in favor of men (FPR: 10% vs. 3%). Men get the benefit, while women face stricter standards.
- Racial Bias: The model is 2x less likely to correctly identify wealthy individuals from minority groups compared to dominant groups

### Simply removing "Sex" and "Race" columns didn't work.

The bias scores barely moved because the model "guessed" protected traits through other data. This violates AI Act safety requirements regarding hidden proxies.

### Success of the "No-Proxy" Model

Real fairness was only achieved by removing proxy variables (like Relationship for gender or Native Country for race):
Gender: The opportunity gap was cut by more than half.
Race: The fairness gap dropped from a drastic 0.33 to a much better 0.11.
The Trade-off: Making the system fair reduced overall accuracy from 85% to 81%. This was a deliberate design choice to prioritize ethics over raw performance.

# Structure

1. Loading dataset and preparing dataset
2. Fairness EDA
3. Baseline model training + with dropped proxy variables
   <!-- - logistic regression -->
   - random forest
4. Comparison of results based on metrics


# How to run a notebook?

Create a virtual environment.
Install libraries:
pip install -r requirements.txt
download datset and put into /data folder in root directory.



Wnioseki (pl)
Obie cechy wykazują zbliżony, wysoki poziom braku równości demograficznej.

Analiza metryk wewnątrz podgrup płciowych ujawnia systematyczną stronniczość modelu baseline. Największą dysproporcję widać w Selection Rate (26% dla mężczyzn vs 8% dla kobiet), co wskazuje na wyraźny brak równości demograficznej.

Kluczowym znaleziskiem jest różnica w FPR (False Positive Rate). Model trzykrotnie częściej przypisuje wysokie zarobki mężczyznom, którzy w rzeczywistości zarabiają mało (10%), niż kobietom w analogicznej sytuacji (3%). W połączeniu z niższym współczynnikiem Recall dla kobiet (55% vs 62%), dane sugerują, że model stawia kobietom znacznie wyższą 'poprzeczkę' wejścia do klasy wysokich zarobków, jednocześnie wykazując większy stopień tolerancji na błędy (tzw. kredyt zaufania) wobec grupy mężczyzn

Wysokie DPD (0.17) wynika w dużej mierze z historycznych nierówności obecnych w zbiorze danych (niższy base rate wysokich zarobków u kobiet). Jednak fakt, że model wykazuje niższy Recall (TPR) oraz znacznie niższy FPR dla kobiet niż dla mężczyzn, dowodzi, że algorytm nie tylko odwzorowuje te różnice, ale dodatkowo stawia kobietom surowsze warunki klasyfikacji, co pogłębia istniejącą niesprawiedliwość.

Audyt sprawiedliwości względem rasy ujawnia głębokie nierówności, znacznie wykraczające poza proste różnice w liczebności grup. Wskaźnik EOD (0.33) znajduje odzwierciedlenie w drastycznych różnicach Recall (TPR) – model dwukrotnie rzadziej identyfikuje osoby zamożne w grupach mniejszościowych (Amer-Indian, Other) niż w grupie White czy Asian.

Co więcej, model wykazuje silną stronniczość w zakresie 'kredytu zaufania' (FPR). Osoby o pochodzeniu azjatyckim lub białym mają ok. 3-4 razy większą szansę na optymistyczną pomyłkę modelu niż osoby czarnoskóre czy rdzenni Amerykanie. Sugeruje to, że algorytm baseline utrwala stereotypy rasowe, promując grupy dominujące nie tylko ze względu na ich cechy, ale także poprzez wyższy margines błędu na ich korzyść.
