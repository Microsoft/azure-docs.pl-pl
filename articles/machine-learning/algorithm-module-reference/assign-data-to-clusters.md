---
title: 'Przypisywanie danych do klastra: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu przypisz dane do klastra w Azure Machine Learning do oceny modelu klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: e618395a2a4a11b4afb311d612cf5d0e27503dc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898864"
---
# <a name="module-assign-data-to-clusters"></a>Moduł: przypisywanie danych do klastrów

W tym artykule opisano sposób korzystania z modułu *przypisywanie danych do klastrów* w programie Azure Machine Learning Designer. Moduł generuje przewidywania za pośrednictwem modelu klastrowania, który został przeszkolony przy użyciu *tego algorytmu.*

Moduł przypisz dane do klastrów zwraca zestaw danych, który zawiera prawdopodobne przypisania dla każdego nowego punktu danych. 

## <a name="how-to-use-assign-data-to-clusters"></a>Jak używać przypisywania danych do klastrów
  
1. W projektancie Azure Machine Learning Znajdź wcześniej przeszkolony model klastra. Model klastrowania można utworzyć i wyszkolić przy użyciu jednej z następujących metod:  
  
    - Konfiguruje K-oznacza algorytm klastrowania przy użyciu modułu [k-oznaczanie klastrowania](k-means-clustering.md) i uczenie modelu przy użyciu zestawu danych i modułu klastra usługi Uczenie (ten artykuł).  
  
    - Możesz również dodać istniejący model przeszkolonych klastrów z grupy **zapisane modele** w obszarze roboczym.

2. Dołącz model przeszkolony do lewego portu wejściowego **przypisywania danych do klastrów**.  

3. Dołącz nowy zestaw danych jako dane wejściowe. 

   W tym zestawie danych etykiety są opcjonalne. Ogólnie rzecz biorąc, klastrowanie to nienadzorowana Metoda uczenia się. Nie oczekuje się, że kategorie z góry są znane. Jednak kolumny wejściowe muszą być takie same jak kolumny, które były używane podczas uczenia modelu klastrowania lub wystąpi błąd.

    > [!TIP]
    > Aby zmniejszyć liczbę kolumn, które są zapisywane do projektanta z prognoz klastrów, użyj [opcji wybierz kolumny w zestawie danych](select-columns-in-dataset.md)i wybierz podzestaw kolumn. 
    
4. Pozostaw zaznaczone pole wyboru **Dołącz do dołączenia lub usuń zaznaczenie tylko dla wyniku** , jeśli chcesz, aby wyniki zawierały pełny wejściowy zestaw danych, w tym kolumnę, w której są wyświetlane wyniki (przypisania klastra).
  
    Jeśli wyczyścisz to pole wyboru, tylko wyniki zostaną zwrócone. Ta opcja może być przydatna podczas tworzenia prognoz w ramach usługi sieci Web.
  
5.  Prześlij potok.  
  
### <a name="results"></a>Wyniki

+  Aby wyświetlić wartości w zestawie danych, kliknij prawym przyciskiem myszy moduł, a następnie wybierz polecenie **Wizualizuj**. Lub wybierz moduł i przejdź do karty dane **wyjściowe** w prawym panelu, kliknij ikonę histogramu w danych **wyjściowych portu** , aby wizualizować wynik.

