---
title: 'Wstępnie przetworzony tekst: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyczyścić i uprościć tekst za pomocą wstępnie przetwarzanego modułu tekstu w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/16/2020
ms.openlocfilehash: 366b30df677a5b74bc7d70e1aea60e05b4df0152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94659311"
---
# <a name="preprocess-text"></a>Wstępne przetwarzanie tekstu

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Użyj modułu **tekstu przetwarzania wstępnego** , aby wyczyścić i uprościć tekst. Obsługuje te Typowe operacje przetwarzania tekstu:

* Usuwanie wyrazów Stop
* Wyszukiwanie i zastępowanie określonych ciągów docelowych przy użyciu wyrażeń regularnych
* Lematyzacja, który konwertuje wiele pokrewnych wyrazów na pojedynczą postać kanoniczną
* Normalizacja przypadku
* Usuwanie niektórych klas znaków, takich jak liczby, znaki specjalne i sekwencje powtórzonych znaków, takich jak "aaaa"
* Identyfikowanie i usuwanie wiadomości e-mail i adresów URL

Moduł **tekstu przetwarzania wstępnego** obsługuje obecnie tylko język angielski.

## <a name="configure-text-preprocessing"></a>Konfigurowanie przetwarzania wstępnego tekstu  

1.  Dodaj **wstępnie przetworzony moduł tekstowy** do potoku w Azure Machine Learning. Ten moduł można znaleźć w obszarze **Analiza tekstu**.

1. Połącz zestaw danych, który zawiera co najmniej jedną kolumnę zawierającą tekst.

1. Wybierz język z listy rozwijanej **Język** .

1. **Kolumna tekstowa do oczyszczenia**: wybierz kolumnę, którą chcesz wstępnie przetworzyć.

1. **Usuń słowa Stop**: zaznacz tę opcję, jeśli chcesz zastosować wstępnie zdefiniowaną listę odrzucany termin do kolumny tekst. 

    Listy odrzucany termin są zależne od języka i dostosowywalne.

1. **Lematyzacja**: Wybierz tę opcję, jeśli chcesz, aby wyrazy były reprezentowane w postaci kanonicznej. Ta opcja jest przydatna do zmniejszenia liczby unikatowych wystąpień podobnych tokenów tekstowych.

    Proces Lematyzacja jest wysoce zależny od języka...

1. **Wykryj zdania**: zaznacz tę opcję, jeśli chcesz, aby moduł wstawiał znak graniczny zdania podczas przeprowadzania analizy.

    Ten moduł używa serii trzech znaków potoku `|||` do reprezentowania terminatora zdania.

1. Wykonaj opcjonalne operacje znajdowania i zamieniania przy użyciu wyrażeń regularnych. Wyrażenie regularne zostanie przetworzone w pierwszej kolejności, przed wszystkimi innymi wbudowanymi opcjami.

    * **Niestandardowe wyrażenie regularne**: Zdefiniuj szukany tekst.
    * **Niestandardowy ciąg zamienny**: Zdefiniuj pojedynczą wartość zastępczą.

1. **Normalizing Case do małych liter**: zaznacz tę opcję, jeśli chcesz skonwertować wielką literę ASCII na postać małych liter.

    Jeśli znaki nie są znormalizowane, ten sam wyraz pisany wielkimi literami i małe litery jest uznawany za dwa różne słowa.

1. Można również usunąć następujące typy znaków lub sekwencje znaków z przetworzonego tekstu wyjściowego:

    * **Usuń numery**: zaznacz tę opcję, aby usunąć wszystkie znaki numeryczne dla określonego języka. Numery identyfikacyjne są zależne od domeny i języka. Jeśli znaki numeryczne są integralną częścią znanego wyrazu, numer nie może zostać usunięty. Dowiedz się więcej w temacie [Uwagi techniczne](#technical-notes).
    
    * **Usuń znaki specjalne**: Użyj tej opcji, aby usunąć wszystkie znaki specjalne inne niż alfanumeryczne.
    
    * **Usuń zduplikowane znaki**: zaznacz tę opcję, aby usunąć dodatkowe znaki w każdej sekwencji powtarzanej przez więcej niż dwa razy. Na przykład sekwencja, taka jak "AAAAA", zostanie zredukowana do "AA".
    
    * **Usuń adresy e-mail**— wybierz tę opcję, aby usunąć dowolną sekwencję formatu `<string>@<string>` .  
    * **Usuń adresy URL**: zaznacz tę opcję, aby usunąć sekwencję zawierającą następujące PREFIKSY adresów URL: `http` , `https` , `ftp` , `www`
    
1. **Rozwiń kontrakty czasownikowe**: Ta opcja ma zastosowanie tylko do języków, które używają umów czasownikowych; obecnie tylko w języku angielskim. 

    Na przykład po wybraniu tej opcji można zastąpić frazę " *nie* Zostań *tam"* ".

1. **Normalizowanie ukośników odwrotnych do ukośników**: Wybierz tę opcję, aby zmapować wszystkie wystąpienia elementu `\\` do `/` .

1. **Podziel tokeny na znaki specjalne**: zaznacz tę opcję, jeśli chcesz przerwać słowa na znakach, takich jak `&` , `-` i tak dalej. Ta opcja może również zmniejszyć liczbę znaków specjalnych, gdy powtarza się więcej niż dwa razy. 

    Na przykład ciąg `MS---WORD` zostałby podzielony na trzy tokeny, `MS` , `-` , i `WORD` .

1. Prześlij potok.

## <a name="technical-notes"></a>Uwagi techniczne

Moduł **wstępnego przetwarzania tekstu** w programie Studio (klasyczny) i Projektant używają różnych modeli językowych. W Projektancie jest stosowany model przeszkolony przez wiele zadań CNN z [spaCy](https://spacy.io/models/en). Różne modele zapewniają różne tokenizatora i części mowy moduł tagujący, które prowadzą do różnych wyników.

Poniżej przedstawiono kilka przykładów:

| Konfigurowanie | Wynik wyjściowy |
| --- | --- |
|Ze wszystkimi wybranymi opcjami </br> Wyjaśnienie: </br> W przypadku przypadków takich jak "3test" w "3test 4test", Projektant usunął całe słowo "3test", ponieważ w tym kontekście część-mowę moduł tagujący określa ten token "3test" jako liczbę i zgodnie z częścią mowy, moduł usuwa go.| :::image type="content" source="./media/module/preprocess-text-all-options-selected.png" alt-text="Ze wszystkimi wybranymi opcjami" border="True"::: |
|Tylko z `Removing number` wybranym </br> Wyjaśnienie: </br> W przypadku takich przypadków, jak "3test", "4-we", Projektant tokenizatora nie podzielił tych przypadków i traktuje je jako całe tokeny. Nie spowoduje to usunięcia cyfr z tych wyrazów.| :::image type="content" source="./media/module/preprocess-text-removing-numbers-selected.png" alt-text="Wybrano tylko &quot;usuwanie numeru&quot;" border="True"::: |

Można również użyć wyrażenia regularnego do wyprowadzania dostosowanych wyników:

| Konfigurowanie | Wynik wyjściowy |
| --- | --- |
|Ze wszystkimi wybranymi opcjami </br> Niestandardowe wyrażenie regularne: `(\s+)*(-|\d+)(\s+)*` </br> Niestandardowy ciąg zamienny: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-all-options-selected.png" alt-text="Ze wszystkimi wybranymi opcjami i wyrażeniem regularnym" border="True"::: |
|Tylko z `Removing number` wybranym </br> Niestandardowe wyrażenie regularne: `(\s+)*(-|\d+)(\s+)*` </br> Niestandardowy ciąg zamienny: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-removing-numbers-selected.png" alt-text="Z opcją usuwania cyfr wybranych i wyrażenia regularnego" border="True"::: |


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 