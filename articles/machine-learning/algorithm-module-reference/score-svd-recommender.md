---
title: 'Polecający wskaźnik SVD: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu polecania "Ocena SVD" w Azure Machine Learning do oceny polecanych prognoz dla zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/10/2020
ms.openlocfilehash: bf62fa995724b8e1fff757e89945cc39db3d9842
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893704"
---
# <a name="score-svd-recommender"></a>Wynik modułu poleceń SVD

W tym artykule opisano sposób korzystania z modułu polecania "Ocena SVD" w programie Azure Machine Learning Designer. Ten moduł służy do tworzenia prognoz przy użyciu przeszkolonego modelu rekomendacji opartego na algorytmie z rozkładem pojedynczego wartości (SVD).

Zalecenie SVD może generować dwa różne rodzaje prognoz:

- [Prognozowanie klasyfikacji dla danego użytkownika i elementu](#prediction-of-ratings)
- [Polecanie elementów użytkownikowi](#recommendations-for-users)

Podczas tworzenia drugiego typu prognoz można działać w jednym z następujących trybów:

- **Tryb produkcyjny** traktuje wszystkich użytkowników lub elementy. Zwykle jest używana w usłudze sieci Web.

  Możesz tworzyć wyniki dla nowych użytkowników, nie tylko dla użytkowników oglądanych podczas uczenia się. Aby uzyskać więcej informacji, zobacz [Uwagi techniczne](#technical-notes). 

- **Tryb oceny** działa na zmniejszonym zestawie użytkowników lub elementów, które można ocenić. Jest zazwyczaj używany podczas operacji potoku.

Aby uzyskać więcej informacji na temat algorytmu z zaleceniem SVD, zobacz factorizationing The Research Paper [Matrix for rekomending Systems](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Jak skonfigurować zalecany polecający wskaźnik SVD

Ten moduł obsługuje dwa typy prognoz, z których każdy ma inne wymagania. 

###  <a name="prediction-of-ratings"></a>Prognozowanie klasyfikacji

Podczas przewidywania klasyfikacji model oblicza sposób, w jaki użytkownik reaguje na określony element, pod kątem danych szkoleniowych. Dane wejściowe dla oceny muszą zapewnić zarówno użytkownika, jak i element do oceny.

1. Dodaj przeszkolony model rekomendacji do potoku, a następnie połącz go z **polecanym rozwiązaniem SVD**. Należy utworzyć model przy użyciu modułu [polecający SVD](train-SVD-recommender.md) .

2. W przypadku **typu przewidywania polecania**wybierz pozycję **Prognoza klasyfikacji**. Nie są wymagane żadne inne parametry.

3. Dodaj dane, dla których chcesz dokonać prognoz, i połącz je z **zestawem danych w celu oceny**.

   Aby model przewidzieć klasyfikację, wejściowy zestaw danych musi zawierać pary User-Item.

   Zestaw danych może zawierać opcjonalną trzecią kolumnę klasyfikacji dla pary elementu User-Item w pierwszej i drugiej kolumnie. Ale trzecia kolumna zostanie zignorowana podczas przewidywania.

4. Prześlij potok.

### <a name="results-for-rating-predictions"></a>Wyniki prognoz klasyfikacji 

Wyjściowy zestaw danych zawiera trzy kolumny: Użytkownicy, elementy i Klasyfikacja przewidywane dla każdego wejściowego użytkownika i elementu.

###  <a name="recommendations-for-users"></a>Zalecenia dla użytkowników 

Aby zalecać elementy dla użytkowników, należy podać listę użytkowników i elementów jako dane wejściowe. Z tych danych model używa swojej wiedzy na temat istniejących elementów i użytkowników w celu wygenerowania listy elementów z prawdopodobnym odwołaniem do każdego użytkownika. Można dostosować liczbę zwracanych zaleceń. Można też ustawić próg liczby wcześniejszych zaleceń, które są wymagane do wygenerowania rekomendacji.

1. Dodaj przeszkolony model rekomendacji do potoku, a następnie połącz go z **polecanym rozwiązaniem SVD**.  Należy utworzyć model przy użyciu modułu [polecający SVD](train-svd-recommender.md) .

2. Aby zalecać elementy dla listy użytkowników, należy ustawić **rekomendację** **rodzajową polecania** dla elementu.

3. W przypadku **zalecanego wyboru elementów**wskaż, czy używasz modułu oceniania w środowisku produkcyjnym, czy do oceny modelu. Wybierz jedną z następujących wartości:

    - **Z wszystkich elementów**: Wybierz tę opcję, jeśli konfigurujesz potok do użycia w usłudze sieci Web lub w środowisku produkcyjnym.  Ta opcja włącza *tryb produkcyjny*. Moduł wykonuje zalecenia ze wszystkich elementów widzianych podczas szkolenia.

    - **Z elementów ocenianych (do oceny modelu)**: Wybierz tę opcję, jeśli tworzysz lub testujesz model. Ta opcja włącza *Tryb oceny*. Moduł wykonuje zalecenia tylko z tych elementów w wejściowym zestawie danych, który został sklasyfikowany.
    
    - **Z niesklasyfikowanych elementów (w celu zaproponowania nowych elementów użytkownikom)**: Wybierz tę opcję, jeśli chcesz, aby moduł miał zalecenia tylko z tych elementów w zestawie danych szkoleniowych, które nie zostały ocenione. 

4. Dodaj zestaw danych, dla którego chcesz dokonać prognoz, a następnie połącz go z **zestawem danych w celu oceny**.

    - Dla **wszystkich elementów**wejściowy zestaw danych powinien zawierać jedną kolumnę. Zawiera identyfikatory użytkowników, dla których należy wykonać zalecenia.

      Zestaw danych może zawierać dodatkowe dwie kolumny identyfikatorów i ocen elementów, ale te dwie kolumny są ignorowane. 

    - W przypadku **elementów ocenianych (dla oceny modelu)** wejściowy zestaw danych powinien zawierać pary elementu User-Item. Pierwsza kolumna powinna zawierać identyfikator użytkownika. Druga kolumna powinna zawierać odpowiednie identyfikatory elementów.

      Zestaw danych może zawierać trzecią kolumnę klasyfikacji elementów użytkownika, ale ta kolumna jest ignorowana.

    - Dla **elementów niesklasyfikowanych (w celu zaproponowania nowych elementów użytkownikom)** wejściowy zestaw danych powinien składać się z par elementów użytkownika. Pierwsza kolumna powinna zawierać identyfikator użytkownika. Druga kolumna powinna zawierać odpowiednie identyfikatory elementów.

     Zestaw danych może zawierać trzecią kolumnę klasyfikacji elementów użytkownika, ale ta kolumna jest ignorowana.

5. **Maksymalna liczba elementów, które mają być zalecane dla użytkownika**: wprowadź liczbę elementów do zwrócenia dla każdego użytkownika. Domyślnie moduł zaleca pięć elementów.

6. **Minimalny rozmiar puli rekomendacji na użytkownika**: wprowadź wartość wskazującą, ile wcześniejszych rekomendacji są wymagane. Domyślnie ten parametr jest ustawiony na 2, co oznacza, że element jest zalecany dla co najmniej dwóch innych użytkowników.

   Tej opcji należy używać tylko wtedy, gdy ocenia się tryb oceny. Opcja jest niedostępna, jeśli wybrano opcję **spośród wszystkich elementów** lub **niesklasyfikowanych elementów (w celu zaproponowania nowych elementów użytkownikom)**.

7.  W przypadku **niesklasyfikowanych elementów (w celu zaproponowania nowych elementów użytkownikom)** Użyj trzeciego portu wejściowego o nazwie **dane szkoleniowe**, aby usunąć elementy, które zostały już ocenione z wyników przewidywania.

    Aby zastosować ten filtr, Połącz oryginalny zestaw danych szkoleniowych z portem wejściowym.

8. Prześlij potok.

### <a name="results-of-item-recommendation"></a>Wyniki zalecenia dotyczącego elementu

Wynikowy zestaw danych zwracany przez zalecaną listę zalecanych elementów dla każdego użytkownika:

- Pierwsza kolumna zawiera identyfikatory użytkowników.
- Jest generowanych kilka dodatkowych kolumn, w zależności od wartości ustawionej na **maksymalną liczbę elementów, które mają być zalecane dla użytkownika**. Każda kolumna zawiera zalecany element (według identyfikatora). Zalecenia są uporządkowane według koligacji elementu User-Item. Element o najwyższej koligacji jest umieszczany w **elemencie kolumny 1**.


##  <a name="technical-notes"></a>Uwagi techniczne

Jeśli masz potok z zaleceniem SVD i przenosisz model do środowiska produkcyjnego, pamiętaj, że istnieją kluczowe różnice między używaniem zalecenia w trybie oceny i użyciem go w trybie produkcyjnym.

Ocena zgodnie z definicją wymaga przewidywania, które mogą być zweryfikowane względem *prawdy* w zestawie testów. Podczas oceniania zalecenia należy przewidzieć tylko elementy, które zostały ocenione w zestawie testów. Ogranicza to możliwe wartości, które są przewidywane.

Gdy operacjonalizować model, zazwyczaj zmienia się tryb prognozowania, aby zalecenia były wykonywane na podstawie wszystkich możliwych elementów, aby uzyskać najlepsze przewidywania. W przypadku wielu z tych prognoz nie ma odpowiedniej rzeczywistości. Nie można zweryfikować dokładności zalecenia w taki sam sposób jak podczas operacji potoku.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
