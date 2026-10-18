# Six Star Finishers Analysis

[English version](README.md)

[Zobacz interaktywny raport Power BI](https://app.fabric.microsoft.com/view?r=eyJrIjoiZGFmZWRkOWMtZTE2Zi00MTM2LTkwODctNmRjZGRmOWVlMmVmIiwidCI6IjI3ZWI2YjZjLWRhZWQtNDFkMS05NTJhLTM0ZDY0MjgzZWFhNSJ9&embedImagePlaceholder=true)

## Opis projektu

Projekt przedstawia analizę **27 964 Six Star Finishers**, czyli zawodników, którzy ukończyli wszystkie sześć oryginalnych maratonów należących do cyklu Abbott World Marathon Majors:

- Tokyo Marathon
- Boston Marathon
- London Marathon
- Berlin Marathon
- Chicago Marathon
- New York City Marathon

Analiza obejmuje **167 784 wyników maratońskich** zawodników reprezentujących **146 krajów i terytoriów**.

Projekt łączy **Python, Power BI, Power Query i DAX**. Python został wykorzystany do pobrania, przygotowania i walidacji danych, natomiast Power BI do stworzenia modelu danych, przeprowadzenia analiz i przygotowania interaktywnego raportu.

Głównym celem projektu było zbadanie:

- jak zmieniała się liczba Six Star Finishers w kolejnych latach,
- które kraje i terytoria mają największą reprezentację,
- jak zmienia się udział kobiet i mężczyzn wśród zawodników,
- ile czasu upływa między pierwszym a ostatnim zarejestrowanym wynikiem maratońskim,
- które maratony najczęściej występują jako pierwsze i ostatnie w zapisanych wynikach zawodników,
- na których maratonach zawodnicy osiągali swoje najlepsze czasy,
- jak rozkładają się czasy ukończenia poszczególnych maratonów,
- jak duże są różnice między najszybszym a najwolniejszym wynikiem danego zawodnika.

Raport składa się z **pięciu interaktywnych stron**, obejmujących ogólny przegląd danych, geografię, trendy i strukturę płci, długość zarejestrowanego okresu oraz analizę wyników maratońskich.

## Źródło danych

Dane pochodzą z oficjalnego **Abbott World Marathon Majors Six Star Hall of Fame**.

**Źródło:** https://www.worldmarathonmajors.com/six-star/hall-of-fame

**Data pobrania:** 6 października 2026  
**Data publikacji projektu:** 16 października 2026  
**Zakres wyników:** 1978–2026, do maratonu w Berlinie włącznie

Najważniejsze informacje o zbiorze:

- **Six Star Finishers:** 27 964
- **Marathon Results:** 167 784
- **Countries & Territories:** 146
- **Median Recorded Span:** 6,6 roku
- **Median Finish Time:** 03:55:35

Każdemu zawodnikowi przypisano po jednym zapisanym wyniku z każdego z sześciu maratonów. Dane nie stanowią pełnej historii wszystkich startów maratońskich tych osób.

**Ważne ograniczenie źródła:** Hall of Fame nie zawiera informacji pozwalającej rozróżnić biegaczy od zawodników na wózkach sportowych. Obie grupy zostały uwzględnione w analizie, mimo że ich czasy ukończenia maratonów nie są bezpośrednio porównywalne.

### Kontekst historyczny

Pierwszy **maraton w Londynie odbył się w 1981 roku**, a **w Tokio w 2007 roku**. Tokio dołączyło do cyklu World Marathon Majors w **2013 roku**, dlatego wszystkie wykresy trendów rozpoczynają się od tego roku.

Wyróżnienie **Six Star** zostało wprowadzone w **2016 roku**.

W **2020 roku**, w związku z pandemią COVID-19, jedynie Tokio i Londyn zorganizowały maratony z udziałem wyłącznie elity sportowej.

### Status danych za 2026 rok

Dane za **2026 rok są niepełne** i obejmują wyniki do maratonu w Berlinie w 2026 roku.

**Chicago Marathon odbył się 11 października 2026 roku**, jednak jego wyniki nie zostały jeszcze uwzględnione w wykorzystanym zbiorze danych. **New York City Marathon odbędzie się 1 listopada 2026 roku**.

**Planowana jest aktualizacja raportu po New York City Marathon 2026**, gdy odpowiednie dane będą dostępne w Hall of Fame.

## Pobieranie i przygotowanie danych w Pythonie

Do pozyskania i przygotowania danych wykorzystano **Python w środowisku Google Colab**.

[Zobacz notebook z przygotowaniem danych](Python/six_star_finishers.ipynb)

### Pobieranie danych

Dane pobrano za pomocą biblioteki `requests` z publicznego endpointu wyszukiwania Hall of Fame.

W zapytaniu zastosowano warunek `total_stars = 6`, ograniczający zbiór do osób posiadających komplet sześciu gwiazdek.

Ponieważ liczba rekordów przekraczała standardowy limit 10 000 wyników, wykorzystano **paginację `search_after`**, umożliwiającą pobieranie danych partiami po **1000 rekordów**.

Podczas pobierania:

- zachowano kolejność rekordów według identyfikatora zawodnika,
- kontrolowano liczbę wyników deklarowaną przez źródło,
- porównywano ją z liczbą pobranych rekordów,
- sprawdzano kompletność pobierania.

Pobrano **27 964 rekordy zawodników**.

### Przygotowanie danych

Do przetworzenia danych wykorzystano bibliotekę `pandas`.

Dane źródłowe zostały rozdzielone na dwie powiązane tabele:

**`finishers`**

Tabela zawierająca informacje o zawodnikach, między innymi identyfikatory, płeć oraz reprezentowany kraj lub terytorium.

**`marathon_results`**

Tabela zawierająca po sześć wyników maratońskich przypisanych do każdego zawodnika, w tym nazwę maratonu, datę biegu i czas ukończenia.

Podczas przygotowania danych:

- zachowano identyfikatory zawodników i rekordów źródłowych,
- uporządkowano nazwy i strukturę pól,
- przypisano pełne nazwy krajów i terytoriów na podstawie słownika źródłowego,
- zachowano oryginalną postać czasów ukończenia,
- przeliczono czasy na sekundy do dalszych obliczeń,
- dodano znacznik czasu pobrania danych.

### Walidacja danych

Przed eksportem przeprowadzono kontrolę jakości i spójności danych.

Sprawdzono między innymi:

- zgodność liczby rekordów ze źródłem,
- unikalność identyfikatorów,
- obecność sześciu różnych maratonów dla każdego zawodnika,
- poprawność powiązań między zawodnikami i ich wynikami,
- brakujące i nieprawidłowe wartości,
- poprawność dat oraz czasów maratońskich,
- zgodność końcowej liczby wyników.

Po zakończeniu walidacji otrzymano:

- **27 964 zawodników**
- **167 784 wyniki maratońskie**

Wszystkie kontrole jakości zakończyły się pomyślnie.

### Eksport danych

Przygotowane dane wyeksportowano do dwóch plików CSV:

- `finishers.csv`
- `marathon_results.csv`

Pliki te zostały następnie wykorzystane do budowy modelu danych w Power BI.

### Przygotowanie kalendarza maratonów

Oddzielnie przygotowano dodatkowy plik CSV zawierający daty poszczególnych edycji sześciu maratonów. **Wszystkie daty zostały zweryfikowane** przed wykorzystaniem ich w raporcie. Kalendarz umożliwia analizę wyników według konkretnego maratonu, daty jego rozegrania oraz roku.

## Model danych

Dane zaimportowano do **Power BI**, gdzie przygotowano relacyjny model oparty na tabeli faktów i tabelach wymiarów.

**Tabela faktów:**

- **`fMarathon_Results`** – wyniki sześciu maratonów przypisanych do każdego zawodnika.

**Tabele wymiarów:**

- **`dFinishers`** – informacje o zawodnikach Six Star Finishers.
- **`dMarathons`** – wymiar maratonów.
- **`dMarathon_Dates`** – daty poszczególnych edycji maratonów.
- **`dCalendar`** – kalendarz umożliwiający analizy w czasie.

Model pozwala analizować dane według zawodnika, kraju lub terytorium, płci, maratonu i roku.

W Power Query oraz modelu danych przygotowano również pola wspierające grupowanie, filtrowanie, sortowanie i obliczenia analityczne.

## Miary DAX

W raporcie opracowano miary DAX wykorzystywane do analiz oraz dynamicznych porównań.

Obliczenia obejmują między innymi:

- liczbę zawodników i wyników maratońskich,
- liczbę reprezentowanych krajów i terytoriów,
- udział krajów w całym zbiorze,
- udział kobiet i mężczyzn,
- liczbę zawodników według roku najpóźniejszego zapisanego wyniku,
- medianę i średnią okresu między pierwszym a ostatnim wynikiem,
- medianę czasu ukończenia maratonu,
- wyniki kobiet i mężczyzn według maratonu i roku,
- udział wyników poniżej trzech godzin,
- najczęściej występujący najszybszy maraton,
- różnice między najszybszym a najwolniejszym wynikiem,
- porównanie czasów pierwszego i ostatniego zapisanego maratonu.

Miary umożliwiają analizę danych w różnych kontekstach filtrowania i porównywanie wybranych grup zawodników.

## Strony raportu

### 1. Overview

Strona Overview stanowi wprowadzenie do analizy i prezentuje najważniejsze informacje o Six Star Finishers.

Główne wskaźniki:

- Six Star Finishers
- Marathon Results
- Countries & Territories
- Median Journey
- Median Finish Time

Strona przedstawia również:

- strukturę zawodników według płci,
- pięć krajów i terytoriów z największą liczbą Six Star Finishers,
- udział kobiet i mediany czasów w tych krajach,
- kalendarz sześciu maratonów,
- roczne zmiany liczby zawodników według najpóźniejszego zapisanego roku wyniku.

Zawiera także informacje o historii World Marathon Majors, źródle danych i ograniczeniach metodologicznych.

![Overview](Screenshots/01_overview.png)

### 2. Geography

Strona Geography koncentruje się na geograficznym zróżnicowaniu Six Star Finishers.

Umożliwia analizę:

- liczby zawodników z poszczególnych krajów i terytoriów,
- procentowego udziału krajów w całym zbiorze,
- liczby reprezentowanych krajów w kolejnych latach,
- udziału kobiet wśród zawodników z 15 najliczniej reprezentowanych krajów,
- zmian liczby zawodników dla wybranego kraju,
- mediany i średniej okresu między pierwszym a ostatnim wynikiem,
- mediany czasów maratońskich,
- liczby zawodników w latach 2024 i 2025 oraz zmian rok do roku.

Interaktywne filtry pozwalają porównywać dane dla wybranego kraju lub terytorium.

![Geography](Screenshots/02_geography.png)

### 3. Trends & Gender

Strona Trends & Gender przedstawia zmiany w czasie oraz różnice między kobietami i mężczyznami.

Analiza obejmuje:

- roczną liczbę wyników zapisanych dla każdego z sześciu maratonów,
- liczbę kobiet i mężczyzn według najpóźniejszego zapisanego roku wyniku,
- zmiany procentowego udziału kobiet,
- medianę i średnią okresu między pierwszym a ostatnim wynikiem,
- medianę czasów maratońskich kobiet i mężczyzn,
- porównanie czasów na poszczególnych maratonach według roku rozegrania biegu.

W analizie rozróżniono rok rozegrania konkretnego maratonu oraz rok najpóźniejszego wyniku przypisanego zawodnikowi.

![Trends & Gender](Screenshots/03_trends_gender.png)

### 4. Six Star Journey

Strona Six Star Journey analizuje okres między pierwszym a ostatnim zarejestrowanym wynikiem maratońskim zawodnika.

Najważniejsze wskaźniki:

- **Median Recorded Span:** 6,6 roku
- **Shortest Recorded Span:** 204 dni
- **Longest Recorded Span:** 45,9 roku

Strona przedstawia:

- rozkład zawodników według długości tego okresu,
- medianę okresu dla 15 najliczniej reprezentowanych krajów,
- najczęściej występujące pierwsze i ostatnie maratony,
- ranking dziesięciu najdłuższych zarejestrowanych okresów,
- kombinacje pierwszego i ostatniego maratonu.

Przełącznik First Marathon / Last Marathon umożliwia zmianę perspektywy analizy.

Przedstawiony okres wynika z dostępnych sześciu wyników i nie musi odpowiadać pełnej historii startów zawodnika.

![Six Star Journey](Screenshots/04_six_star_journey.png)

### 5. Marathon Results

Strona Marathon Results koncentruje się na analizie wyników maratońskich Six Star Finishers.

Przedstawia:

- porównanie czasów pierwszego i ostatniego zarejestrowanego maratonu,
- udział wyników poniżej trzech godzin na każdym maratonie,
- liczbę zawodników, których mediana czasu ukończenia sześciu zarejestrowanych maratonów przekracza cztery godziny,
- procentowy udział wszystkich wyników maratońskich przekraczających cztery godziny,
- rozkład różnic między najszybszym a najwolniejszym zarejestrowanym wynikiem każdego zawodnika,
- ranking maratonów, na których zawodnicy najczęściej osiągali swoje najlepsze czasy,
- procentowy rozkład czasów ukończenia poszczególnych maratonów,
- szczegółowe porównanie najszybszych i najwolniejszych wyników zawodników, wraz z rokiem rozegrania każdego bie

![Marathon Results](Screenshots/05_marathon_results.png)

## Najważniejsze wnioski

Analiza pozwoliła zidentyfikować kilka istotnych prawidłowości:

1. **Stany Zjednoczone mają największą reprezentację**, liczącą 7 923 zawodników, czyli 28,3% analizowanego zbioru.

2. **Udział kobiet zwiększa się w kolejnych latach**, osiągając około 40% w 2026 roku.

3. **Mediana okresu między pierwszym a ostatnim zapisanym wynikiem wynosi 6,6 roku**, jednak indywidualne wartości są bardzo zróżnicowane.

4. **New York City najczęściej występuje jako pierwszy zapisany maraton** w zestawie sześciu wyników zawodnika.

5. **Chicago i Berlin najczęściej są maratonami, na których zawodnicy uzyskali swój najlepszy czas**, odpowiednio dla 6 377 i 6 097 osób.

6. **Najczęściej występująca różnica między najszybszym a najwolniejszym wynikiem wynosi od 30 do 60 minut**.

7. **Większość zawodników osiągnęła wolniejszy czas w ostatnim zapisanym maratonie niż w pierwszym**.

8. **Berlin ma najwyższy udział wyników poniżej trzech godzin**, wynoszący 8,9%.

Wnioski odnoszą się wyłącznie do zawodników i wyników dostępnych w analizowanym zbiorze Six Star Hall of Fame.

## Metodologia i ograniczenia

Przy interpretacji wyników należy uwzględnić ograniczenia źródła.

### Zakres analizy

Raport obejmuje wyłącznie zawodników znajdujących się w Six Star Hall of Fame, a nie wszystkich uczestników maratonów World Marathon Majors.

### Rok ukończenia Six Star (Completion Year)

Rok przypisany zawodnikowi jest wyznaczany na podstawie **najpóźniejszego roku spośród jego sześciu zapisanych wyników**.

Nie musi być to rzeczywisty rok pierwszego ukończenia wszystkich sześciu maratonów ani otrzymania medalu Six Star.

### Długość wyzwania (Journey Duration)

Długość zarejestrowanego okresu jest obliczana jako różnica między datą pierwszego i ostatniego wyniku dostępnego w zbiorze.

Nie oznacza pełnej historii kariery sportowej zawodnika.

### Płeć (Gender)

Źródło zawiera niewielką liczbę rekordów z nieokreśloną kategorią płci. Analizy udziału kobiet i mężczyzn wykorzystują grupy Female oraz Male.

### Czasy ukończenia maratonów (Finish Times)

Czasy ukończenia maratonów obejmują również zawodników na wózkach sportowych. Ze względu na brak identyfikacji kategorii sportowej nie można rozdzielić ich wyników od wyników biegaczy.

### Aktualizacje danych

Hall of Fame jest źródłem aktualizowanym w czasie. Ponowne pobranie danych może zwrócić inną liczbę rekordów.

Wszystkie wyniki w raporcie odnoszą się do zbioru pobranego **6 października 2026 roku**.

## Narzędzia i technologie

- **Python** – pobranie, przygotowanie i walidacja danych
- **requests** – pobieranie danych z publicznego endpointu
- **pandas** – przekształcanie i eksport danych
- **Google Colab** – środowisko pracy z notebookiem
- **Power BI Desktop** – modelowanie, analiza i wizualizacja
- **Power Query** – przygotowanie danych do modelu
- **DAX** – miary analityczne i dynamiczne obliczenia
- **CSV** – przygotowanie i wymiana danych
- **GitHub** – dokumentacja projektu i prezentacja rezultatów

## Struktura repozytorium

```text
six-star-finishers-analysis/
│
├── Python/
│   ├── six_star_finishers.ipynb
│   └── requirements.txt
│
├── Screenshots/
│   ├── 01_overview.png
│   ├── 02_geography.png
│   ├── 03_trends_gender.png
│   ├── 04_six_star_journey.png
│   └── 05_marathon_results.png
│
├── README.md
└── README_PL.md
```

Notebook `six_star_finishers.ipynb` dokumentuje proces pobrania, przygotowania i walidacji danych.

Plik `requirements.txt` zawiera biblioteki niezbędne do uruchomienia notebooka.

Folder `Screenshots` zawiera obrazy pięciu stron raportu Power BI.

## Źródło

**Abbott World Marathon Majors – Six Star Hall of Fame**

https://www.worldmarathonmajors.com/six-star/hall-of-fame

**Data pobrania:** 6 października 2026  
**Data publikacji projektu:** 16 października 2026  
**Zakres raportu:** do maratonu w Berlinie w 2026 roku

Planowana jest aktualizacja raportu po udostępnieniu danych z pozostałych maratonów rozegranych w 2026 roku.
