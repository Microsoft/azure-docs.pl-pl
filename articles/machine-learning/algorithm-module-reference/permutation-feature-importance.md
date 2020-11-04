---
title: 'Ważność funkcji permutacji: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu ważności funkcji permutacji w Azure Machine Learning, aby obliczyć wyniki ważności funkcji permutacji zmiennych funkcji, pod kątem modelu przeszkolonego i zestawu danych testowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: bddd9a6825899f646a86ae274c539b54156667c5
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319396"
---
# <a name="permutation-feature-importance"></a>Ważność funkcji permutacji

W tym artykule opisano, jak używać modułu ważności funkcji permutacji w programie Azure Machine Learning Designer do obliczania zestawu wyników ważności funkcji dla zestawu danych. Te wyniki służą do określenia najlepszych funkcji do użycia w modelu.

W tym module wartości funkcji są losowo przebierane w jedną kolumnę w czasie. Wydajność modelu jest mierzona przed i po. Możesz wybrać jedną ze standardowych metryk, aby zmierzyć wydajność.

Wyniki zwracane przez moduł reprezentują *zmianę* wydajności przeszkolonego modelu, po permutacji. Ważne funkcje są zwykle bardziej wrażliwe na proces Shuffling, więc będą powodować wyższe znaczenie. 

Ten artykuł zawiera omówienie funkcji permutacji, jej teoretyczną podstawę i jej aplikacji w usłudze Machine Learning: [ważność funkcji permutacji](/archive/blogs/machinelearning/permutation-feature-importance).  

## <a name="how-to-use-permutation-feature-importance"></a>Jak używać ważności funkcji permutacji

Generowanie zestawu wyników funkcji wymaga, aby był już szkolony model, a także zestaw danych testowych.  

1.  Dodaj moduł ważności funkcji permutacji do potoku. Ten moduł można znaleźć w kategorii **wybór funkcji** . 

2.  Połącz model szkolony z lewej strony. Model musi być modelem regresji lub modelem klasyfikacji.  

3.  Po prawej stronie Wprowadź połączenie zestawu danych. Najlepiej wybrać taki, który jest inny niż zestaw danych, który był używany do uczenia modelu. Ten zestaw danych jest używany do oceniania na podstawie nauczonego modelu. Jest on również używany do oceny modelu po zmianie wartości funkcji.  

4.  W przypadku **losowego inicjatora** wprowadź wartość, która ma być używana jako inicjator losowy. Jeśli określisz wartość 0 (wartość domyślna), jest generowana liczba na podstawie zegara systemowego.

     Wartość inicjatora jest opcjonalna, ale należy podać wartość, jeśli chcesz, aby odtwarzalność w przebiegach tego samego potoku.  

5.  Aby uzyskać **metrykę mierzenia wydajności** , wybierz jedną metrykę, która ma być używana podczas obliczania jakości modelu po permutacji.  

     Azure Machine Learning Designer obsługuje następujące metryki, w zależności od tego, czy oceniasz model klasyfikacji czy regresji:  

    -   **Klasyfikacja**

        Dokładność, precyzja, odwołanie  

    -   **Regresja**

        Precyzja, odwoływanie, średnie Błędy bezwzględne, główny, średni, błąd względny  

     Bardziej szczegółowy opis tych metryk oceny i sposobu ich obliczania można znaleźć w temacie [Oceń model](evaluate-model.md).  

6.  Prześlij potok.  

7.  Moduł generuje listę kolumn funkcji i skojarzonych z nimi ocen. Lista jest uporządkowana w kolejności malejącej.  


##  <a name="technical-notes"></a>Uwagi techniczne

Ważność funkcji permutacji działa przez losowe zmiany wartości każdej kolumny funkcji, jednej kolumny w danym momencie. Następnie oblicza model. 

Klasyfikacje zapewniane przez moduł są często różne od tych, które są uzyskiwane z [wyboru funkcji opartych na filtrowaniu](filter-based-feature-selection.md). Wybór funkcji oparty na filtrze oblicza wyniki *przed* utworzeniem modelu. 

Przyczyna różnic polega na tym, że ważność funkcji permutacji nie mierzy skojarzenia między funkcją a wartością docelową. Zamiast tego przechwytuje wpływ poszczególnych funkcji na przewidywania z modelu.
  
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.