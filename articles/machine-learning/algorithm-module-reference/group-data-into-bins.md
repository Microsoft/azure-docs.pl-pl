---
title: 'Grupowanie danych w pojemnikach: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu dane grupy do modułów do grupowania numerów lub zmiany dystrybucji danych ciągłych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: de9f4517d134b4a42603291110c6b19f69403412
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907044"
---
# <a name="group-data-into-bins-module"></a>Grupowanie danych w moduł pojemników

W tym artykule opisano sposób używania modułu dane grupy do modułów w programie Azure Machine Learning Designer do grupowania numerów lub zmiany dystrybucji danych ciągłych.

Moduł dane grupy do zasobników obsługuje wiele opcji danych pakowania. Możesz dostosować sposób ustawiania krawędzi pojemnika i sposobu, w jaki wartości są rozdzielane do zasobników. Można na przykład:  

+ Ręcznie wpisz serię wartości do obsłużenia jako granice pojemnika.  
+ Przypisz wartości do pojemników przy użyciu *quantiles*lub rangi percentylu.  
+ Wymuś równomierny rozkład wartości w pojemnikach.  

## <a name="more-about-binning-and-grouping"></a>Więcej informacji na temat pakowania i grupowania

*Pakowania* lub grupowanie danych (czasami nazywane *podziału*) jest ważnym narzędziem do przygotowywania danych liczbowych do uczenia maszynowego. Jest to przydatne w scenariuszach takich jak:

+ Kolumna liczb ciągłych ma zbyt wiele unikatowych wartości do modelowania efektywnie. W związku z tym automatycznie lub ręcznie Przypisz wartości do grup, aby utworzyć mniejszy zestaw zakresów dyskretnych.

+ Chcesz zamienić kolumnę liczb na wartości kategorii reprezentujące określone zakresy.

    Na przykład możesz chcieć grupować wartości w kolumnie wiek przez określenie zakresów niestandardowych, takich jak 1-15, 16-22, 23-30 i tak dalej w przypadku wartości demograficznych użytkownika.

+ Zestaw danych ma kilka skrajnych wartości, a wszystko to poza oczekiwanym zakresem, a te wartości mają wpływ na rozmiar przeszkolonego modelu. Aby wyeliminować odchylenia w modelu, można przekształcić dane w jednorodną dystrybucję przy użyciu metody quantiles.

    W przypadku tej metody moduł dane grupy do przedziałów określa idealne lokalizacje pojemników i szerokości pojemników, aby zapewnić, że około tej samej liczby próbek należy do każdego pojemnika. Następnie w zależności od wybranej metody normalizacji wartości w pojemnikach są przekształcane do percentylów lub mapowane na numer pojemnika.

### <a name="examples-of-binning"></a>Przykłady pakowania

Na poniższym diagramie przedstawiono rozkład wartości liczbowych przed i po pakowania za pomocą metody *quantiles* . Zwróć uwagę, że w porównaniu z danymi pierwotnymi po lewej stronie dane zostały Binned i przekształcone na skalę normalną.  

Możesz znaleźć [przykład z wyniku tego uruchomienia potoku](https://ml.azure.com/visualinterface/authoring/Normal/87270db9-4651-448e-bd28-8ef7428084dc?wsid=%2Fsubscriptions%2Fe9b2ec51-5c94-4fa8-809a-dc1e695e4896%2Fresourcegroups%2Fmodule-ws-rg%2Fworkspaces%2Fmodule-prerelease-119&flight=cm&tid=72f988bf-86f1-41af-91ab-2d7cd011db47&smtendpoint=https%3A%2F%2Fsmt-test1.azureml-test.net).

Ze względu na to, że istnieje wiele sposobów grupowania danych, wszystkie możliwe do dostosowania, zalecamy eksperymentowanie z różnymi metodami i wartościami. 

## <a name="how-to-configure-group-data-into-bins"></a>Jak skonfigurować dane grupy w pojemnikach

1. Dodaj **dane grupy do modułu zasobników** do potoku w projektancie. Ten moduł można znaleźć w kategorii **Przekształcanie danych**.

2. Połącz zestaw danych, który zawiera dane liczbowe do bin. Podziału można stosować tylko do kolumn zawierających dane liczbowe. 

    Jeśli zestaw danych zawiera kolumny nieliczbowe, użyj modułu [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) , aby wybrać podzbiór kolumn, z którymi chcesz współpracować.

3. Określ tryb pakowania. Tryb pakowania określa inne parametry, dlatego należy najpierw wybrać opcję **trybu pakowania** . Obsługiwane są następujące typy pakowania:

    - **Quantiles**: Metoda quantile przypisuje wartości do pojemników na podstawie rangi percentylu. Ta metoda jest również znana jako o równej wysokości pakowania.

    - **Równa szerokość**: w przypadku tej opcji należy określić łączną liczbę pojemników. Wartości z kolumny dane są umieszczane w pojemnikach, w taki sposób, że każdy pojemnik ma ten sam interwał między wartością początkową i końcową. W związku z tym niektóre pojemniki mogą mieć więcej wartości, jeśli dane są clumped wokół określonego punktu.

    - **Krawędzie niestandardowe**: możesz określić wartości, które zaczynają się w każdym pojemniku. Wartość brzegowa jest zawsze dolną granicą pojemnika. 
    
      Załóżmy na przykład, że chcesz grupować wartości w dwóch pojemnikach. Wartość jednego z nich będzie większa niż 0, a jedna z nich będzie zawierać wartości mniejsze lub równe 0. W tym przypadku dla krawędzi pojemników należy wprowadzić **0** w **rozdzielaną przecinkami listę krawędzi pojemnika**. Dane wyjściowe modułu będą 1 i 2 wskazujące indeks pojemnika dla każdej wartości wiersza. Należy zauważyć, że lista wartości rozdzielanych przecinkami musi być w kolejności rosnącej, takiej jak 1, 3, 5, 7.

4. Jeśli używasz trybów pakowania **quantiles** i **równych szerokości** , użyj opcji **Liczba pojemników** , aby określić liczbę pojemników lub *quantiles*, które chcesz utworzyć.

5. W przypadku **kolumn na bin**Użyj selektora kolumn, aby wybrać kolumny zawierające wartości, które chcesz umieścić w koszu. Kolumny muszą być typu danych liczbowych.

    Ta sama reguła pakowania jest stosowana do wszystkich odpowiednich kolumn, które można wybrać. Jeśli konieczne jest przedzielenie niektórych kolumn za pomocą innej metody, użyj oddzielnego wystąpienia danych grupy do modułu zasobników dla każdego zestawu kolumn.

    > [!WARNING]
    > W przypadku wybrania kolumny, która nie jest dozwolonym typem, generowany jest błąd czasu wykonywania. Moduł zwraca błąd zaraz po znalezieniu dowolnej kolumny niedozwolonego typu. Jeśli wystąpi błąd, przejrzyj wszystkie wybrane kolumny. Błąd nie wyświetla wszystkich nieprawidłowych kolumn.

6. W polu **tryb wyjściowy**wskaż, w jaki sposób chcesz wyprowadzać wartości Quantized:

    + **Dołącz**: tworzy nową kolumnę z wartościami Binned i dołącza je do tabeli wejściowej.

    + **Miejsce**: zamienia oryginalne wartości na nowe wartości w zestawie danych.

    + **ResultOnly**: zwraca tylko kolumny wynikowe.

7. W przypadku wybrania trybu **quantiles** pakowania Użyj opcji **normalizacji quantile** , aby określić, jak wartości są znormalizowane przed sortowaniem do quantiles. Należy zauważyć, że normalizacja wartości przekształca wartości, ale nie wpływa na końcową liczbę pojemników.

    Obsługiwane są następujące typy normalizacji:

    + **Procent**: wartości są znormalizowane w zakresie [0100].

    + **PQuantile**: wartości są znormalizowane w zakresie [0, 1].

    + **QuantileIndex**: wartości są znormalizowane w zakresie [1, liczba pojemników].

8. W przypadku wybrania opcji **niestandardowe krawędzie** wprowadź rozdzieloną przecinkami listę liczb, które mają być używane jako *krawędzie pojemnika* na liście pól tekstowych **rozdzielonych przecinkami** . 

    Wartości oznaczają punkt dzielący pojemniki. Jeśli na przykład wprowadzisz jedną wartość krawędzi bin, zostaną wygenerowane dwa pojemniki. Jeśli wprowadzisz dwie wartości krawędzi bin, zostaną wygenerowane trzy zasobniki.

    Wartości muszą być sortowane w kolejności tworzenia pojemników — od najniższego do najwyższego.

10. Wybierz opcję **kolumny tagów jako kategorii** , aby wskazać, że kolumny Quantized mają być obsługiwane jako zmienne kategorii.

11. Prześlij potok.

## <a name="results"></a>Wyniki

Moduł dane grupy do pojemników zwraca zestaw danych, w którym każdy element został Binned zgodnie z określonym trybem. 

Zwraca również *transformację pakowania*. Tę funkcję można przesłać do modułu [Zastosuj transformację](apply-transformation.md) , aby zabin nowe przykłady danych przy użyciu tego samego trybu pakowania i parametrów.  

> [!TIP]
> Jeśli używasz pakowania na danych szkoleniowych, musisz użyć tej samej metody pakowania dla danych, które są używane do testowania i przewidywania. Należy również użyć tych samych lokalizacji pojemników i szerokości pojemników. 
> 
> Aby zapewnić, że dane są zawsze przekształcane przy użyciu tej samej metody pakowania, zalecamy zapisanie przydatnych transformacji danych. Następnie zastosuj je do innych zestawów danych przy użyciu modułu [zastosowania przekształcenia](apply-transformation.md) .

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
