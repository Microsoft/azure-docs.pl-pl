---
title: Grupowanie danych w pojemnikach
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu dane grupy do modułów do grupowania numerów lub zmiany dystrybucji danych ciągłych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: b33aa3d2ab22fc986100d6141dd03d5547ef1862
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853827"
---
# <a name="group-data-into-bins"></a>Grupowanie danych w pojemnikach

W tym artykule opisano sposób używania modułu [dane grupy do](group-data-into-bins.md) modułów w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu grupowania numerów lub zmiany dystrybucji danych ciągłych.

Moduł [dane grupy do zasobników](group-data-into-bins.md) obsługuje wiele opcji danych pakowania. Możesz dostosować sposób ustawiania krawędzi pojemnika i sposobu, w jaki wartości są rozdzielane do zasobników. Można na przykład:  

+ Ręcznie wpisz serię wartości do obsłużenia jako granice pojemnika.  
+ Przypisz wartości do pojemników przy użyciu *quantiles*lub rangi percentylu.  
+ Wymuś równomierny rozkład wartości w pojemnikach.  

### <a name="more-about-binning-and-grouping"></a>Więcej informacji na temat pakowania i grupowania

*Pakowania* lub grupowanie danych (czasami nazywane *podziału*) jest ważnym narzędziem do przygotowywania danych liczbowych do uczenia maszynowego i jest przydatne w scenariuszach takich jak:

+ Kolumna liczb ciągłych ma zbyt wiele unikatowych wartości do modelowania efektywnie, dlatego można automatycznie lub ręcznie przypisać wartości do grup, aby utworzyć mniejszy zestaw zakresów dyskretnych.

+ Zastąp kolumnę liczb wartością kategorii reprezentującą określone zakresy.

    Na przykład możesz chcieć grupować wartości w kolumnie wiek przez określenie zakresów niestandardowych, takich jak 1-15, 16-22, 23-30 i tak dalej w przypadku wartości demograficznych użytkownika.

+ Zestaw danych ma kilka skrajnych wartości, a wszystko to poza oczekiwanym zakresem, a te wartości mają wpływ na rozmiar przeszkolonego modelu. Aby wyeliminować odchylenia w modelu, można przekształcić dane w jednolitą dystrybucję przy użyciu metody quantiles.

    W przypadku tej metody moduł [dane grupy do przedziałów](group-data-into-bins.md) określa idealne lokalizacje pojemników i szerokości pojemników, aby zapewnić, że około tej samej liczby próbek należy do każdego pojemnika. Następnie, w zależności od wybranej metody normalizacji, wartości w pojemnikach są przekształcone na percentyle lub mapowane na numer pojemnika.

### <a name="examples-of-binning"></a>Przykłady pakowania

Na poniższym diagramie przedstawiono rozkład wartości liczbowych przed i po pakowania za pomocą metody **quantiles** . Zwróć uwagę, że w porównaniu z danymi pierwotnymi po lewej stronie dane zostały Binned i przekształcone na skalę normalną.  

"Przykład można znaleźć w wyniku tego uruchomienia potoku: https://ml.azure.com/visualinterface/authoring/Normal/87270db9-4651-448e-bd28-8ef7428084dc?wsid=%2Fsubscriptions%2Fe9b2ec51-5c94-4fa8-809a-dc1e695e4896%2Fresourcegroups%2Fmodule-ws-rg%2Fworkspaces%2Fmodule-prerelease-119&flight=cm&tid=72f988bf-86f1-41af-91ab-2d7cd011db47&smtendpoint=https%3A%2F%2Fsmt-test1.azureml-test.net "

Ze względu na to, że istnieje wiele sposobów grupowania danych, wszystkie możliwe do dostosowania, zalecamy eksperymentowanie z różnymi metodami i wartościami. 

## <a name="how-to-configure-group-data-into-bins"></a>Jak skonfigurować dane grupy w pojemnikach

1. Dodaj **dane grupy do** modułu do potoku w projektancie (wersja zapoznawcza). Ten moduł można znaleźć w kategorii **Przekształcanie danych**.

2. Połącz zestaw danych, który zawiera dane liczbowe do bin.  Podziału można stosować tylko do kolumn zawierających dane liczbowe. 

    Jeśli zestaw danych zawiera kolumny nieliczbowe, użyj modułu [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) , aby wybrać podzbiór kolumn, z którymi chcesz współpracować.

3. Określ tryb pakowania. Tryb pakowania określa inne parametry, dlatego należy najpierw wybrać opcję **trybu pakowania** . Obsługiwane są następujące typy pakowania:

    **Quantiles**: Metoda quantile przypisuje wartości do pojemników na podstawie rangi percentylu. Quantiles jest również znany jako równe wysokość pakowania.

    **Równa szerokość**: w przypadku tej opcji należy określić łączną liczbę pojemników. Wartości z kolumny dane są umieszczane w pojemnikach, w taki sposób, że każdy pojemnik ma ten sam interwał między wartością początkową i końcową. W związku z tym niektóre pojemniki mogą mieć więcej wartości, jeśli dane są clumped wokół określonego punktu.

    **Krawędzie niestandardowe**: możesz określić wartości, które zaczynają się w każdym pojemniku. Wartość brzegowa jest zawsze dolną granicą pojemnika.  Załóżmy na przykład, że chcesz grupować wartości w dwa pojemnikach, jeden z wartościami większymi od 0 i jeden z wartościami mniejszą lub równą 0. W tym przypadku dla krawędzi pojemników należy wpisać 0 w **rozdzielaną przecinkami listą krawędzi pojemnika**. Dane wyjściowe modułu byłyby 1 i 2 wskazujące indeks pojemnika dla każdej wartości wiersza. Należy pamiętać, że lista wartości rozdzielanych przecinkami musi znajdować się w kolejności rosnącej, np. 1, 3, 5, 7.

4. **Liczba pojemników**: Jeśli używasz **quantiles**i trybów **równości** pakowania, Użyj tej opcji, aby określić liczbę pojemników lub *quantiles*, które chcesz utworzyć.

5. W przypadku **kolumn na bin**Użyj selektora kolumn, aby wybrać kolumny zawierające wartości, które chcesz umieścić w koszu. Kolumny muszą być typu danych liczbowych.

    Ta sama reguła pakowania jest stosowana do wszystkich odpowiednich kolumn, które można wybrać. W związku z tym, jeśli konieczne jest przedzielenie niektórych kolumn za pomocą innej metody, należy użyć osobnego wystąpienia [danych grupy do pojemników](group-data-into-bins.md) dla każdego zestawu kolumn.

    > [!WARNING]
    > W przypadku wybrania kolumny, która nie jest dozwolonym typem, generowany jest błąd czasu wykonywania. Moduł zwraca błąd zaraz po znalezieniu dowolnej kolumny niedozwolonego typu. Jeśli wystąpi błąd, przejrzyj wszystkie wybrane kolumny. Błąd nie wyświetla wszystkich nieprawidłowych kolumn.

6. W polu **tryb wyjściowy**wskaż, w jaki sposób chcesz wyprowadzać wartości Quantized.

      + **Append**: tworzy nową kolumnę z wartościami Binned i dołącza je do tabeli wejściowej.

      + **Miejsce**: zamienia oryginalne wartości na nowe wartości w zestawie danych.

      + **ResultOnly**: zwraca tylko kolumny wynikowe.

7. W przypadku wybrania trybu **quantiles** pakowania Użyj opcji **normalizacji quantile** , aby określić, jak wartości są znormalizowane przed sortowaniem do quantiles. Należy zauważyć, że normalizacja wartości przekształca wartości, ale nie wpływa na ostateczną liczbę pojemników.

    Obsługiwane są następujące typy normalizacji:

    + **Procent**: wartości są znormalizowane w zakresie [0100]

    + **PQuantile**: wartości są znormalizowane w zakresie [0, 1]

    + **QuantileIndex**: wartości są znormalizowane w zakresie [1, liczba pojemników]

8. W przypadku wybrania opcji **niestandardowe krawędzie** wpisz rozdzieloną przecinkami listę liczb, które mają być używane jako *krawędzie pojemnika* w polu tekstowym **separatory pojemników + rozdzielane przecinkami** .  Wartości oznaczają punkt dzielący pojemniki, w związku z czym w przypadku wpisania jednej wartości krawędzi bin zostanie wygenerowany dwa pojemniki; Jeśli wpiszesz dwie wartości krawędzi bin, zostaną wygenerowane trzy zasobniky i tak dalej.

    Wartości muszą być sortowane w kolejności, w jakiej są tworzone, od najniższego do najwyższego.

10. **Oznacz kolumny jako kategorii**: Wybierz tę opcję, aby wskazać, że kolumny Quantized mają być obsługiwane jako zmienne kategorii.

11. Prześlij potok.

### <a name="results"></a>Wyniki

Moduł [dane grupy do pojemników](group-data-into-bins.md) zwraca zestaw danych, w którym każdy element został Binned zgodnie z określonym trybem. 

Zwraca również **transformację pakowania**, która jest funkcją, którą można przesłać do modułu [Zastosuj transformację](apply-transformation.md) , aby zabin nowe przykłady danych przy użyciu tego samego trybu pakowania i parametrów.  

> [!TIP]
> Pamiętaj, że jeśli używasz pakowania na danych szkoleniowych, musisz użyć tej samej metody pakowania dla danych, które są używane do testowania i przewidywania. Obejmuje to metodę pakowania, lokalizacje pojemników i szerokości pojemników. 
> 
> Aby upewnić się, że dane są zawsze przekształcane przy użyciu tej samej metody pakowania, zalecamy zapisanie przydatnych transformacji danych, a następnie zastosowanie ich do innych zestawów, przy użyciu modułu [zastosowania przekształcenia](apply-transformation.md) .

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
