---
title: Ocenianie modelu Vowpal Wabbit
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu oceny Vowpal Wabbit wygenerować wyniki dla zestawu danych wejściowych przy użyciu istniejącego wyszkolonego modelu Vowpal Wabbit.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898399"
---
# <a name="score-vowpal-wabbit-model"></a>Ocenianie modelu Vowpal Wabbit
W tym artykule opisano, jak używać modułu **oceny Vowpal Wabbit** w programie Azure Machine Learning Designer do generowania wyników dla zestawu danych wejściowych przy użyciu istniejącego wyszkolonego modelu Vowpal Wabbit.  

Ten moduł zawiera najnowszą wersję Vowpal platformy Wabbit Framework w wersji 8.8.1. Ten moduł służy do oceny danych przy użyciu przeszkolonego modelu zapisanego w formacie VW w wersji 8.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Jak skonfigurować model oceny Vowpal Wabbit

1.  Dodaj moduł **Vowpal Wabbit model oceny** do eksperymentu.  
  
2.  Dodaj szkolony model Wabbit Vowpal, a następnie połącz go z portem wejściowym po lewej stronie. Możesz użyć nauczonego modelu utworzonego w tym samym doświadczeniu lub znaleźć zapisany model w kategorii **zestawy danych** w lewym okienku nawigacji projektanta. Jednak model musi być dostępny w programie Azure Machine Learning Designer.  
  
    > [!NOTE]
    > Obsługiwane są tylko modele Vowpal Wabbit 8.8.1; nie można nawiązać połączenia z zapisanymi modelami, które zostały przeszkolone przy użyciu innych algorytmów.
  
3.  Dodaj testowy zestaw danych i podłącz go do portu wejściowego z prawej strony. Jeśli test DataSet jest katalogiem, który zawiera plik danych testowych, określ nazwę pliku danych testowych z **nazwą pliku danych testowych**. Jeśli test DataSet jest pojedynczym plikiem, pozostaw **nazwę pliku danych testowych** , który ma być pusty.

4. W polu tekstowym **argumenty VW** wpisz zestaw prawidłowych argumentów wiersza polecenia do pliku wykonywalnego Vowpal Wabbit.  

    Aby uzyskać informacje o tym, które argumenty Vowpal Wabbit są obsługiwane i nieobsługiwane w Azure Machine Learning, zobacz sekcję [Uwagi techniczne](#technical-notes) .  

5.  **Nazwa pliku danych testowych**: wpisz nazwę pliku, który zawiera dane wejściowe. Ten argument jest używany tylko wtedy, gdy zestaw danych testowych jest katalogiem.

6. **Określ typ pliku**: wskazuje, który format jest używany przez dane szkoleniowe. Vowpal Wabbit obsługuje te dwa formaty plików wejściowych:  

   - **VW** reprezentuje wewnętrzny format używany przez Vowpal Wabbit. Aby uzyskać szczegółowe informacje, zobacz [stronę typu wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
   - **SVMLight** to format używany przez inne narzędzia do uczenia maszynowego. 

7. Wybierz opcję, **Dołącz dodatkową kolumnę zawierającą etykiety**, jeśli chcesz, aby etykiety były wyprowadzane razem z wynikami.  

   Zwykle podczas obsługi danych tekstowych Vowpal Wabbit nie wymaga etykiet i zwróci tylko wyniki dla każdego wiersza danych.  

8. Wybierz opcję, **Dołącz dodatkową kolumnę zawierającą nieprzetworzone wyniki**, jeśli chcesz, aby wyniki pierwotne były wyprowadzane razem z wynikami.  

9. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wizualizować wyniki, kliknij prawym przyciskiem myszy dane wyjściowe modułu [Vowpal oceny Wabbit](score-vowpal-wabbit-model.md) . Dane wyjściowe wskazują wynik przewidywania znormalizowany od 0 do 1. 

+ Aby oszacować wyniki, wyjściowy zestaw danych powinien zawierać określone nazwy kolumn oceny, które spełniają wymagania modułu oceny modelu.

  + Dla zadania regresji zestaw danych do oceny musi mieć jedną kolumnę o nazwie `Regression Scored Labels` , która reprezentuje etykiety z wynikami.
  + W przypadku zadania klasyfikacji binarnej zestaw danych do oceny musi mieć dwie kolumny o nazwie, `Binary Class Scored Labels` `Binary Class Scored Probabilities` która reprezentuje odpowiednio etykiety i prawdopodobieństwa.
  + W przypadku zadania o wielu klasyfikacjach zestaw danych do oceny musi mieć jedną kolumnę o nazwie `Multi Class Scored Labels` , która reprezentuje etykiety z wynikami.

  Należy zauważyć, że nie można bezpośrednio ocenić wyników modułu modelu Wabbit wyniku oceny Vowpal. Przed dokonaniem oceny należy zmodyfikować zestaw danych zgodnie z powyższymi wymaganiami.

##  <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

### <a name="parameters"></a>Parametry

Vowpal Wabbit ma wiele opcji wiersza polecenia do wybierania i dostrajania algorytmów. W tym miejscu nie można wykonać pełnej dyskusji na temat tych opcji; Zalecamy wyświetlenie [strony typu wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).  

Następujące parametry nie są obsługiwane w Azure Machine Learning Studio (klasyczny).  

-   Opcje wejścia/wyjścia określone w [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Te właściwości są już konfigurowane automatycznie przez moduł.  
  
-   Ponadto każda opcja, która generuje wiele wyjść lub pobiera wiele danych wejściowych, jest niedozwolona. Należą *`--cbt`* do nich, *`--lda`* i *`--wap`* .  
  
-   Obsługiwane są tylko algorytmy uczenia nadzorowanego. Uniemożliwia to następujące opcje: *`–active`* , `--rank` *`--search`* itp.  

Wszystkie argumenty inne niż opisane powyżej są dozwolone.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 