---
title: 'Podziel dane: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu Split Data w Azure Machine Learning, aby podzielić zestaw danych na dwa różne zestawy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a4c93b12ad654e54a7f3c7ee0e75507d5cb45e90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907824"
---
# <a name="split-data-module"></a>Moduł Split Data

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Użyj modułu Split Data, aby podzielić zestaw danych na dwa różne zestawy.

Ten moduł jest przydatny, gdy trzeba podzielić dane na zestawy szkoleniowe i testowe. Można również dostosować sposób, w jaki dane są dzielone. Niektóre opcje obsługują generowanie losowe danych. Inne są dostosowane do określonego typu danych lub typu modelu.

## <a name="configure-the-module"></a>Konfigurowanie modułu

> [!TIP]
> Przed wybraniem trybu dzielenia, Przeczytaj wszystkie opcje, aby określić typ potrzebnego podziału.
> W przypadku zmiany trybu dzielenia wszystkie inne opcje mogą zostać zresetowane.

1. Dodaj moduł **Split Data (podział danych** ) do potoku w projektancie. Ten moduł można znaleźć w obszarze **Przekształcanie danych** w kategorii **przykład i Podziel** .

1. **Tryb dzielenia**: Wybierz jeden z następujących trybów, w zależności od typu danych i sposobu, w jaki chcesz go podzielić. Każdy tryb dzielenia ma inne opcje.

   - **Podziel wiersze**: Użyj tej opcji, jeśli chcesz podzielić dane na dwie części. Możesz określić wartość procentową danych, która ma zostać umieszczona w każdym podziale. Domyślnie dane są podzielone na 50/50.

     Możesz również losowo wybrać wiersze w każdej grupie i użyć próbkowania stratified. W przypadku próbkowania stratified należy wybrać pojedynczą kolumnę danych, dla której wartości mają być rozdzielane równo między dwa zestawy danych wynikowych.  

   - **Podział wyrażenia regularnego**: Wybierz tę opcję, jeśli chcesz podzielić zestaw danych przez testowanie pojedynczej kolumny dla wartości.

     Na przykład w przypadku analizowania tonacji można sprawdzić obecność nazwy konkretnego produktu w polu tekstowym. Następnie można podzielić zestaw danych na wiersze z docelową nazwą produktu i wierszami bez docelowej nazwy produktu.

   - **Podzielone wyrażenie względne**: Użyj tej opcji, jeśli chcesz zastosować warunek do kolumny liczb. Liczba może być polem daty/godziny, kolumną zawierającą kwoty wieku lub dolara, a nawet wartością procentową. Na przykład możesz chcieć podzielić zestaw danych na podstawie kosztu elementów, grupować osoby według zakresów wieku lub oddzielić dane według daty kalendarzowej.

### <a name="split-rows"></a>Podziel wiersze

1. Dodaj moduł [Split Data (podział danych](./split-data.md) ) do potoku w projektancie, a następnie połącz zestaw danych, który chcesz podzielić.
  
1. W obszarze **tryb dzielenia** wybierz pozycję **podział wierszy**. 

1. **Ułamek wierszy w pierwszym wyjściowym zestawie danych**: Użyj tej opcji, aby określić, ile wierszy zostanie umieszczonych w pierwszych (po lewej stronie). Wszystkie pozostałe wiersze zostaną umieszczone w drugim (prawej stronie) danych wyjściowych.

   Współczynnik reprezentuje procent wierszy wysyłanych do pierwszego wyjściowego zestawu danych, więc należy wprowadzić liczbę dziesiętną z przedziału od 0 do 1.
     
   Na przykład jeśli wprowadzisz **0,75** jako wartość, zestaw danych zostanie podzielony 75/25. W tym podziale 75 procent wierszy zostanie wysłany do pierwszego wyjściowego zestawu danych. Pozostałe 25 procent zostanie wysłane do drugiego wyjściowego zestawu danych.
  
1. Wybierz opcję **Losowa Split (Wyodrębnij** ), jeśli chcesz losowo wybierać dane w dwóch grupach. Jest to preferowana opcja podczas tworzenia szkoleń i testów zestawów danych.

1. **Losowy inicjator**: Podaj nieujemną liczbę całkowitą, aby rozpocząć sekwencję pseudolosowych wystąpień do użycia. Ten domyślny inicjator jest używany we wszystkich modułach generujących liczby losowe. 

   Określenie inicjatora powoduje, że wyniki są odtwarzalne. Jeśli trzeba powtórzyć wyniki operacji podziału, należy określić inicjator dla generatora liczb losowych. W przeciwnym razie losowy inicjator jest domyślnie ustawiony na wartość **0**, co oznacza, że początkowa wartość inicjatora jest uzyskiwana z zegara systemowego. W związku z tym Dystrybucja danych może być nieco inna przy każdym wykonaniu podziału. 

1. **Stratified**: Ustaw tę opcję na **wartość true** , aby upewnić się, że dwa wyjściowe zestawy danych zawierają reprezentatywną próbę wartości w kolumnie *strata* lub *kolumnie klucza stratyfikacji*. 

   W przypadku próbkowania stratified dane są dzielone w taki sposób, że każdy wyjściowy zestaw danych uzyskuje się w nierównej części każdej wartości docelowej. Można na przykład upewnić się, że Twoje zestawy szkoleniowe i testowe są zrównoważone w odniesieniu do wyniku lub do innej kolumny (na przykład płci).

1. Prześlij potok.


## <a name="select-a-regular-expression"></a>Wybierz wyrażenie regularne

1. Dodaj moduł [Split Data](./split-data.md) do potoku, a następnie połącz go jako dane wejściowe z zestawem danych, który chcesz podzielić.  
  
1. W obszarze **tryb dzielenia** wybierz pozycję **podział wyrażenia regularnego**.

1. W polu **wyrażenie regularne** wprowadź prawidłowe wyrażenie regularne. 
  
   Wyrażenie regularne powinno przestrzegać składni języka Python dla wyrażeń regularnych.

1. Prześlij potok.

   W oparciu o wprowadzone wyrażenie regularne zestaw danych jest podzielony na dwa zestawy wierszy: wiersze z wartościami, które pasują do wyrażenia i wszystkie pozostałe wiersze. 

W poniższych przykładach pokazano, jak podzielić zestaw danych za pomocą opcji **wyrażenia regularnego** . 

### <a name="single-whole-word"></a>Pojedyncze całe słowo 

Ten przykład umieszcza w pierwszym zestawie danych wszystkie wiersze, które zawierają tekst `Gryphon` w kolumnie `Text` . Umieszcza inne wiersze w drugim danych wyjściowych z **podziałów**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Podciąg

Ten przykład szuka określonego ciągu w każdej pozycji w drugiej kolumnie zestawu danych. Pozycja jest określana tutaj przez wartość indeksu 1. W dopasowaniu jest rozróżniana wielkość liter.

```text
(\1) ^[a-f]
```

Pierwszy wynikowy zestaw danych zawiera wszystkie wiersze, w których kolumna indeksu zaczyna się od jednego z następujących znaków: `a` ,,,, `b` `c` `d` `e` `f` . Wszystkie inne wiersze są kierowane do drugiego danych wyjściowych.

## <a name="select-a-relative-expression"></a>Wybierz wyrażenie względne

1. Dodaj moduł [Split Data](./split-data.md) do potoku, a następnie połącz go jako dane wejściowe z zestawem danych, który chcesz podzielić.
  
1. W obszarze **tryb dzielenia** wybierz pozycję **wyrażenie względne**.
  
1. W polu **wyrażenie relacyjne** wprowadź wyrażenie, które wykonuje operację porównania dla pojedynczej kolumny.

   Dla **kolumny liczbowej**:
   - Kolumna zawiera numery dowolnego typu danych liczbowych, w tym typy danych daty i godziny.
   - Wyrażenie może odwoływać się do maksymalnie jednej nazwy kolumny.
   - Użyj znaku handlowego " `&` i" dla operacji i. Użyj znaku potoku, `|` dla operacji lub.
   - Obsługiwane są następujące operatory: `<` ,,,, `>` `<=` `>=` `==` , `!=` .
   - Nie można grupować operacji za pomocą `(` i `)` .
   
   Dla **kolumny ciągów**:
   - Obsługiwane są następujące operatory: `==` , `!=` .

1. Prześlij potok.

   Wyrażenie dzieli zestaw danych na dwa zestawy wierszy: wiersze z wartościami, które spełniają warunek i wszystkie pozostałe wiersze.

W poniższych przykładach pokazano, jak podzielić zestaw danych za pomocą opcji **wyrażenie względne** w module **Split Data** .  

### <a name="calendar-year"></a>Rok kalendarzowy

Typowym scenariuszem jest dzielenie zestawu danych według lat. Poniższe wyrażenie wybiera wszystkie wiersze, w których wartości w kolumnie `Year` są większe niż `2010` .

```text
\"Year" > 2010
```

Wyrażenie Date musi uwzględniać wszystkie części daty, które są uwzględnione w kolumnie dane. Format dat w kolumnie danych musi być spójny. 

Na przykład w kolumnie daty używającej formatu `mmddyyyy` wyrażenie powinno wyglądać następująco:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Indeks kolumny

W poniższym wyrażeniu pokazano, jak można użyć indeksu kolumn, aby zaznaczyć wszystkie wiersze w pierwszej kolumnie zestawu danych, które zawierają wartości mniejsze niż lub równe 30, ale nie równe 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
