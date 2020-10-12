---
title: 'Normalizuj dane: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu normalizowanie danych w Azure Machine Learning, aby przekształcić zestaw danych za pomocą *normalizacji*.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: de0a23ca9dea210d91fe259b06622226549ba3b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890441"
---
# <a name="normalize-data-module"></a>Normalizowanie modułu danych

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Użyj tego modułu do przekształcenia zestawu danych za pomocą *normalizacji*.

Normalizacja to technika często stosowana w ramach przygotowywania danych do uczenia maszynowego. Celem normalizacji jest zmiana wartości kolumn liczbowych w zestawie danych w celu użycia wspólnej skali bez zakłócania różnic między zakresami wartości ani utraty informacji. Normalizacja jest również wymagana w przypadku niektórych algorytmów w celu poprawnego modelowania danych.

Załóżmy na przykład, że wejściowy zestaw danych zawiera jedną kolumnę z wartościami z zakresu od 0 do 1 i inną kolumną z wartościami z zakresu od 10 000 do 100 000. Znacząca różnica w *skali* liczb może spowodować problemy podczas próby połączenia wartości jako funkcje podczas modelowania.

*Normalizacja* eliminuje te problemy, tworząc nowe wartości, które zachowują ogólną dystrybucję i współczynniki w danych źródłowych, przy jednoczesnym zachowaniu wartości w skali stosowanej we wszystkich kolumnach liczbowych używanych w modelu.

Ten moduł oferuje kilka opcji przekształcania danych liczbowych:

- Można zmienić wszystkie wartości na skalę 0-1 lub przekształcić wartości, reprezentując je jako rangi percentyla, a nie wartości bezwzględne.
- Można zastosować normalizację do pojedynczej kolumny lub do wielu kolumn w tym samym zestawie danych.
- Jeśli trzeba powtórzyć potok lub zastosować te same kroki normalizacji do innych danych, można zapisać kroki jako transformację normalizacji i zastosować je do innych zestawów danych, które mają ten sam schemat.

> [!WARNING]
> Niektóre algorytmy wymagają, aby dane były znormalizowane przed uczeniem modelu. Inne algorytmy wykonują własne skalowanie lub normalizację danych. Dlatego po wybraniu algorytmu uczenia maszynowego, który ma być używany podczas tworzenia modelu predykcyjnego, należy zapoznać się z wymaganiami dotyczącymi danych algorytmu przed zastosowaniem normalizacji do danych szkoleniowych.

##  <a name="configure-normalize-data"></a>Konfigurowanie danych normalizowania

Przy użyciu tego modułu można zastosować tylko jedną metodę normalizacji. W związku z tym ta sama metoda normalizacji jest stosowana do wszystkich zaznaczonych kolumn. Aby użyć różnych metod normalizacji, użyj drugiego wystąpienia **normalizacji danych**.

1. Dodaj moduł **normalizowanie danych** do potoku. Moduł można znaleźć w Azure Machine Learning, w obszarze **Przekształcanie danych**, w kategorii **skalowanie i zmniejszanie** .

2. Połącz zestaw danych, który zawiera co najmniej jedną kolumnę zawierającą wszystkie liczby.

3. Użyj selektora kolumn, aby wybrać kolumny liczbowe do normalizacji. Jeśli nie wybierzesz pojedynczych kolumn, domyślnie zostaną uwzględnione **wszystkie** kolumny typu liczbowego w danych wejściowych, a ten sam proces normalizacji jest stosowany do wszystkich zaznaczonych kolumn. 

    Może to prowadzić do nieoczekiwanych wyników, jeśli dołączysz kolumny liczbowe, które nie powinny być znormalizowane! Zawsze sprawdzaj kolumny.

    Jeśli nie wykryto żadnych kolumn liczbowych, sprawdź metadane kolumny, aby sprawdzić, czy typ danych kolumny jest obsługiwanym typem liczbowym.

    > [!TIP]
    > Aby upewnić się, że kolumny określonego typu są dostarczane jako dane wejściowe, spróbuj użyć modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych](./select-columns-in-dataset.md) ), aby **znormalizować dane**.

4. **Po zaznaczeniu pola wyboru Użyj wartości 0 dla stałych kolumn**: zaznacz tę opcję, jeśli dowolna kolumna liczbowa zawiera pojedynczą, niezmieniającą się wartość. Gwarantuje to, że takie kolumny nie będą używane w operacjach normalizacji.

5. Z listy rozwijanej **Metoda transformacji** wybierz pojedynczą funkcję matematyczną, która ma zostać zastosowana do wszystkich zaznaczonych kolumn. 
  
    - **Zscore**: konwertuje wszystkie wartości na wynik z.
    
      Wartości w kolumnie są przekształcane przy użyciu następującej formuły:  
  
      ![Normalizacja przy użyciu wyników&#45;z](media/module/aml-normalization-z-score.png)
  
      Odchylenie średnie i standardowe są obliczane osobno dla każdej kolumny. Użyto odchylenia standardowego populacji.
  
    - **MinMax**: niestandardowa wartość normalizacji liniowo skaluje każdą funkcję do interwału [0, 1].
    
      Ponowne skalowanie do [0, 1] interwału odbywa się przez przesunięcie wartości każdej funkcji tak, aby minimalna wartość wynosi 0, a następnie podzielenie przez nową wartość maksymalną (czyli różnicę między pierwotnymi wartościami maksymalnymi i minimalnymi).
      
      Wartości w kolumnie są przekształcane przy użyciu następującej formuły:  
  
      ![Normalizacja przy użyciu funkcji minimalnej&#45;Max](media/module/aml-normalization-minmax.png "AML_normalization — MinMax")  
  
    - **Logistyka**: wartości w kolumnie są przekształcane przy użyciu następującej formuły:

      ![Formuła normalizacji przez funkcję logistyczną](media/module/aml-normalization-logistic.png "AML_normalization — logistyka")  
  
    - **Logarytmicznie**: Ta opcja konwertuje wszystkie wartości na skalę logarytmiczną.
  
      Wartości w kolumnie są przekształcane przy użyciu następującej formuły:
  
      ![Dziennik formuł&#45;rozkład normalny](media/module/aml-normalization-lognormal.png "AML_normalization — LOGARYTM logarytmiczny")
    
      Tu μ i σ to parametry dystrybucji, obliczone w sposób niezależny od danych jako maksymalne prawdopodobieństwo oszacowania dla każdej kolumny osobno.  
  
    - **Tanh**: wszystkie wartości są konwertowane na tangens hiperboliczny.
    
      Wartości w kolumnie są przekształcane przy użyciu następującej formuły:
    
      ![Normalizacja przy użyciu funkcji tanh](media/module/aml-normalization-tanh.png "AML_normalization — tanh")

6. Prześlij potok lub kliknij dwukrotnie moduł **normalizowanie danych** , a następnie wybierz pozycję **Uruchom wybrane**. 

## <a name="results"></a>Wyniki

Moduł **normalizowanie danych** generuje dwa dane wyjściowe:

- Aby wyświetlić przekształcone wartości, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**.

    Domyślnie wartości są przekształcane na miejsce. Jeśli chcesz porównać przekształcone wartości z oryginalnymi wartościami, użyj modułu [dodawania kolumn](./add-columns.md) do łączenia zestawów danych i wyświetlania kolumn obok siebie.

- Aby zapisać transformację w taki sposób, aby można było zastosować tę samą metodę normalizacji do innego zestawu danych, wybierz moduł, a następnie wybierz pozycję **zarejestruj zestaw danych** na karcie dane **wyjściowe** w prawym panelu.

    Następnie można załadować zapisane przekształcenia z grupy **transformacje** okienka nawigacji po lewej stronie i zastosować je do zestawu danych z tym samym schematem za pomocą [przekształcenia Zastosuj](apply-transformation.md).  


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 