---
title: Korzystanie z modułu & głębokiego polecania oceny
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z & głębokiego polecania oceny w Azure Machine Learning do oceny rekomendacji dla zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/12/2020
ms.openlocfilehash: 9a1a3892e6a47aabd9b5129ca551900494616bc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905164"
---
# <a name="score-wide-and-deep-recommender"></a>Wyniki modułu poleceń Wide and Deep

W tym artykule opisano, jak używać modułu **oceny szerokiej i głębokiego polecania** w programie Azure Machine Learning Designer do tworzenia prognoz opartych na przeszkolonym modelu rekomendacji opartym na szerokiej & głębokiej uczeniu firmy Google.

Szerokie i głębokie zalecenie mogą generować dwa różne rodzaje prognoz:

- [Prognozowanie klasyfikacji dla danego użytkownika i elementu](#predict-ratings)

- [Zalecane elementy dla danego użytkownika](#recommend-items)


Podczas tworzenia ostatniego rodzaju prognoz można działać w trybie *produkcji* lub w *trybie oceny*.

- **Tryb produkcyjny** traktuje wszystkich użytkowników lub elementy i jest zazwyczaj używany w usłudze sieci Web. Możesz tworzyć wyniki dla nowych użytkowników, nie tylko dla użytkowników oglądanych podczas uczenia się. 

- **Tryb oceny** działa na ograniczonym zestawie użytkowników lub elementów, które mogą być oceniane, i jest zazwyczaj używany podczas eksperymentowania.

Więcej szczegółowych informacji na temat szerokiej i szczegółowej części zalecanej oraz jej podstawowego teorii można znaleźć w odpowiednim dokumencie badawczym:  [szerokie & głębokie uczenie w zakresie systemów zalecanych](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-score-wide-and-deep-recommender"></a>Jak skonfigurować szeroką i polecaną ocenę

Ten moduł obsługuje różne typy zaleceń, z których każdy ma inne wymagania. Kliknij link do typu danych i typ zalecenia, które chcesz utworzyć.

+ [Klasyfikacja predykcyjna](#predict-ratings)
+ [Zalecane elementy](#recommend-items)

### <a name="predict-ratings"></a>Klasyfikacja predykcyjna

Podczas przewidywania klasyfikacji model oblicza, w jaki sposób dany użytkownik reaguje na określony element, pod kątem danych szkoleniowych. W związku z tym dane wejściowe dla oceny muszą zapewnić zarówno użytkownika, jak i element do oceny.

1. Dodaj do eksperymentu specjalistyczny, szeroki & model zaleceń, a następnie połącz go z **przeszkolonym i szerokim modelem zaleceń**.  Należy utworzyć model przy użyciu [szerokiego i szczegółowego zalecenia](train-wide-and-deep-recommender.md).

2. **Rodzaj przewidywania polecania**: wybierz opcję **prognozowanie klasyfikacji**. Nie są wymagane żadne dalsze parametry.

3. Dodaj dane, dla których chcesz dokonać prognoz, i połącz je z **zestawem danych w celu oceny**.

    Aby przewidzieć klasyfikację, wejściowy zestaw danych musi zawierać pary User-Item.

    Zestaw danych może zawierać opcjonalną trzecią kolumnę klasyfikacji dla pary elementu User-Item w pierwszej i drugiej kolumnie, ale trzecia kolumna zostanie zignorowana podczas przewidywania.

4.  (Opcjonalnie). Jeśli masz zestaw danych funkcji użytkownika, połącz go z **funkcjami użytkownika**.

    Zestaw danych funkcji użytkownika powinien zawierać identyfikator użytkownika w pierwszej kolumnie. Pozostałe kolumny powinny zawierać wartości, które opisują użytkowników, takie jak płeć, preferencje, lokalizacja itd.
  
    Funkcje użytkowników, którzy mają sklasyfikowane elementy w zestawie danych szkoleniowych, są ignorowane przez **szerokie i głębokie zalecenie**, ponieważ zostały już uzyskane podczas szkolenia. W związku z tym filtrowanie zestawu danych z wyprzedzeniem w celu uwzględnienia tylko *użytkowników zimnego startu* lub użytkowników, którzy nie oceniły żadnych elementów.

    > [!WARNING]
    > Jeśli model został przeszkolony bez korzystania z funkcji użytkownika, nie można wprowadzać funkcji użytkownika podczas oceniania.

5. Jeśli masz zestaw danych funkcji elementów, możesz połączyć go z **funkcjami elementu**.

    Zestaw danych funkcji elementu musi zawierać identyfikator elementu w pierwszej kolumnie. Pozostałe kolumny powinny zawierać wartości, które opisują elementy.

    Funkcje ocenianych elementów w zestawie danych szkoleniowych są ignorowane przez **szerokie i szczegółowe zalecenia** , ponieważ zostały już uzyskane podczas szkolenia. W związku z tym Ogranicz zestaw danych oceniania do *elementów zimnego startu* lub elementy, które nie zostały ocenione przez żadnych użytkowników.

    > [!WARNING]
    > Jeśli model został przeszkolony bez używania funkcji elementów, nie można wprowadzać funkcji elementów podczas oceniania.

7. Uruchom eksperyment.

### <a name="results-for-rating-predictions"></a>Wyniki prognoz klasyfikacji 

Wyjściowy zestaw danych zawiera trzy kolumny zawierające użytkownika, element oraz klasyfikację przewidywaną dla każdego użytkownika i elementu wejściowego.

Ponadto podczas oceniania są stosowane następujące zmiany:

-  W przypadku kolumny numerycznej funkcji użytkownika lub elementu brakujące wartości są automatycznie zastępowane wartością **średnią** brakujących wartości zestawu szkoleniowego. W przypadku funkcji kategorii brakujące wartości są zastępowane tą samą wartością kategorii inną niż wszystkie możliwe wartości tej funkcji.
-  Żadne tłumaczenie nie jest stosowane do wartości funkcji, aby zachować ich rzadkość.

### <a name="recommend-items"></a>Zalecane elementy

Aby zalecać elementy dla użytkowników, należy podać listę użytkowników i elementów jako dane wejściowe. Z tych danych model używa swojej wiedzy na temat istniejących elementów i użytkowników w celu wygenerowania listy elementów z prawdopodobnym odwołaniem do każdego użytkownika. Można dostosować liczbę zwracanych zaleceń i ustawić próg liczby wcześniejszych zaleceń, które są wymagane w celu wygenerowania rekomendacji.

1. Dodaj przeszkolony i szeroki model zaleceń do eksperymentu i połącz go z **przeszkolonym i szczegółowym modelem rekomendacji**.  Należy utworzyć model przy użyciu [szerokiego i szczegółowego zalecenia](train-wide-and-deep-recommender.md).

2. Aby zalecać elementy dla danej listy użytkowników, należy ustawić opcję **przewidywanie typu predykcyjnego** na **element zalecenie**.

3. **Zalecany wybór elementu**: wskaż, czy używasz modułu oceniania w środowisku produkcyjnym, czy do oceny modelu, wybierając jedną z następujących wartości:

    - **Z elementów ocenianych (na potrzeby oceny modelu)**: Wybierz tę opcję, jeśli tworzysz lub testujesz model. Ta opcja włącza **Tryb oceny**, a moduł wykonuje zalecenia tylko z tych elementów w wejściowym zestawie danych, który został sklasyfikowany.
    - **Z wszystkich elementów**: Wybierz tę opcję, jeśli konfigurujesz eksperyment do użycia w usłudze sieci Web lub w środowisku produkcyjnym.  Ta opcja włącza **tryb produkcyjny**, a moduł wykonuje zalecenia ze wszystkich elementów widzianych podczas szkolenia.
    - **Z niesklasyfikowanych elementów (w celu zaproponowania nowych elementów użytkownikom)**: Wybierz tę opcję, jeśli chcesz, aby moduł miał zalecenia tylko z tych elementów w zestawie danych szkoleniowych, które nie zostały ocenione. 
4. Dodaj zestaw danych, dla którego chcesz dokonać prognoz, a następnie połącz go z **zestawem danych w celu oceny**.

    - Jeśli wybierzesz opcję **z wszystkich elementów**, wejściowy zestaw danych powinien zawierać jeden i tylko jedną kolumnę zawierającą identyfikatory użytkowników, dla których mają być zaleceń.

        Zestaw danych może zawierać dodatkowe dwie kolumny identyfikatorów i ocen elementów, ale te dwie kolumny są ignorowane. 

    - Jeśli wybierzesz opcję **z elementów ocenianych (dla oceny modelu)**, wejściowy zestaw danych powinien składać się z **par elementów użytkownika**. Pierwsza kolumna powinna zawierać identyfikator **użytkownika** . Druga kolumna powinna zawierać odpowiednie identyfikatory **elementów** .

        Zestaw danych może zawierać trzecią kolumnę klasyfikacji elementów użytkownika, ale ta kolumna jest ignorowana.
        
    - Dla **elementów niesklasyfikowanych (w celu zaproponowania nowych elementów użytkownikom)** wejściowy zestaw danych powinien składać się z par elementów użytkownika. Pierwsza kolumna powinna zawierać identyfikator użytkownika. Druga kolumna powinna zawierać odpowiednie identyfikatory elementów.

        Zestaw danych może zawierać trzecią kolumnę klasyfikacji elementów użytkownika, ale ta kolumna jest ignorowana.

5. (Opcjonalnie). Jeśli masz zestaw danych **funkcji użytkownika**, połącz go z **funkcjami użytkownika**.

    Pierwsza kolumna zestawu danych funkcji użytkownika powinna zawierać identyfikator użytkownika. Pozostałe kolumny powinny zawierać wartości charakteryzujące użytkownika, takie jak płeć, preferencje, lokalizacja itd.

    Funkcje użytkowników, którzy mają sklasyfikowane elementy, są ignorowane przez **szerokie i szczegółowe zalecenia**, ponieważ te funkcje zostały już uzyskane podczas szkolenia. W związku z tym można filtrować zestaw danych z wyprzedzeniem w celu uwzględnienia tylko *użytkowników zimnego startu* lub użytkowników, którzy nie ocenili żadnych elementów.

    > [!WARNING]
    >  Jeśli model został przeszkolony bez używania funkcji użytkownika, nie można używać funkcji Apply podczas oceniania.

6. Obowiązkowe Jeśli masz zestaw danych **funkcji elementów**, możesz połączyć go z **funkcjami elementu**.

    Pierwsza kolumna w zestawie danych funkcji elementu musi zawierać identyfikator elementu. Pozostałe kolumny powinny zawierać wartości, które opisują elementy.

    Funkcje elementów ocenianych są ignorowane przez **szeroką i dogłębną zalecaną ocenę**, ponieważ te funkcje zostały już uzyskane podczas uczenia się. W związku z tym można ograniczyć zestaw danych oceniania do *elementów zimnego startu* lub elementów, które nie zostały ocenione przez żadną z użytkowników.

    > [!WARNING]
    >  Jeśli model został przeszkolony bez używania funkcji elementów, nie należy używać funkcji elementów podczas oceniania.  

7. **Maksymalna liczba elementów, które mają być zalecane dla użytkownika**: wpisz liczbę elementów do zwrócenia dla każdego użytkownika. Domyślnie zalecane jest 5 elementów.

8. **Minimalny rozmiar puli rekomendacji na użytkownika**: wpisz wartość wskazującą, ile wcześniejszych rekomendacji są wymagane.  Domyślnie ten parametr ma wartość 2, co oznacza, że element musi być zalecany przez co najmniej dwóch innych użytkowników.

    Tej opcji należy używać tylko wtedy, gdy ocenia się tryb oceny. Opcja jest niedostępna, jeśli wybrano opcję **spośród wszystkich elementów** lub **niesklasyfikowanych elementów (w celu zaproponowania nowych elementów użytkownikom)**.

9. W przypadku **niesklasyfikowanych elementów (w celu zaproponowania nowych elementów użytkownikom)** Użyj trzeciego portu wejściowego o nazwie **dane szkoleniowe**, aby usunąć elementy, które zostały już ocenione z wyników przewidywania.

    Aby zastosować ten filtr, Połącz oryginalny zestaw danych szkoleniowych z portem wejściowym.

10. Uruchom eksperyment.
### <a name="results-of-item-recommendation"></a>Wyniki zalecenia dotyczącego elementu

Wynikowy zestaw danych zwrócony przez **szeroki zakres wyników i zalecaną** listę zalecanych elementów dla każdego użytkownika.

- Pierwsza kolumna zawiera identyfikatory użytkowników.
- Jest generowanych kilka dodatkowych kolumn, w zależności od wartości ustawionej dla **maksymalnej liczby elementów, które mają być zalecane dla użytkownika**. Każda kolumna zawiera zalecany element (według identyfikatora). Zalecenia są uporządkowane według koligacji elementu użytkownika, z elementem o najwyższej koligacji umieszczonym w kolumnie, **element 1**.

##  <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera odpowiedzi na niektóre często zadawane pytania dotyczące używania szerokiej & głębokiego polecania do tworzenia prognoz.  

###  <a name="cold-start-users-and-recommendations"></a>Użytkownicy z zimnym rozpoczęciem i zalecenia

Zazwyczaj, aby utworzyć zalecenia, **cały moduł oceny i szczegółowa polecania** wymagają tych samych danych wejściowych, które były używane podczas uczenia modelu, w tym identyfikatora użytkownika. Jest to spowodowane tym, że algorytm musi wiedzieć, czy zapoznajesz coś dotyczącego tego użytkownika podczas uczenia się. 

Jednak w przypadku nowych użytkowników użytkownik może nie mieć identyfikatora użytkownika, tylko niektórych funkcji użytkownika, takich jak wiek, płeć i tak dalej.

Nadal możesz tworzyć zalecenia dla użytkowników, którzy są nowym systemem, przez obsługiwanie ich jako *użytkowników zimnego startu*. W przypadku takich użytkowników algorytm rekomendacji nie używa przeszłej historii ani poprzednich ocen, tylko funkcji użytkownika.

W celach przewidywania użytkownik o zimnym uruchomieniu jest definiowany jako użytkownik o IDENTYFIKATORze, który nie został użyty do szkolenia. Aby upewnić się, że identyfikatory nie są zgodne z identyfikatorami używanymi w szkoleniu, można utworzyć nowe identyfikatory. Na przykład można generować losowe identyfikatory w określonym zakresie lub przydzielać serię identyfikatorów z wyprzedzeniem dla użytkowników zimnego startu.

Niemniej jednak, jeśli nie masz żadnych danych związanych z filtrowaniem do współpracy, takich jak wektor funkcji użytkownika, lepiej jest używać klasyfikacji lub dowiedzenia regresji.

###  <a name="production-use-of-the-wide-and-deep-recommender"></a>Wykorzystanie w środowisku produkcyjnym szerokiej i głębokiej zalecenia

Jeśli przejdziesz do szerokiego i głębokiego zalecenia, a następnie przeniesiesz model do środowiska produkcyjnego, weź pod uwagę te kluczowe różnice w przypadku korzystania z programu polecania w trybie oceny i w trybie produkcyjnym:

- Ocena zgodnie z definicją wymaga przewidywania, które mogą być zweryfikowane względem *prawdy* w zestawie testów. W związku z tym podczas oceny zalecenia musi on przewidzieć tylko elementy, które zostały sklasyfikowane w zestawie testów. Taka konieczność ogranicza możliwe wartości, które są przewidywane.

    Jednak podczas operacjonalizować modelu zwykle zmienia się tryb prognozowania, aby zalecenia były wykonywane na podstawie wszystkich możliwych elementów, aby uzyskać najlepsze przewidywania. W przypadku wielu z tych prognoz nie ma odpowiednich podstaw, dlatego nie można zweryfikować jego dokładności w taki sam sposób jak podczas eksperymentowania.

- Jeśli nie podasz identyfikatora użytkownika w środowisku produkcyjnym i udostępnisz tylko wektor funkcji, możesz uzyskać jako odpowiedź listę wszystkich zaleceń dla wszystkich możliwych użytkowników. Należy podać identyfikator użytkownika.

    Aby ograniczyć liczbę zwracanych zaleceń, można również określić maksymalną liczbę elementów zwracanych przez użytkownika. 



## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych](module-reference.md) dla Azure Machine Learning. 
