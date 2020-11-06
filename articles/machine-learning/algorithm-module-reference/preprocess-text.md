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
ms.date: 09/01/2019
ms.openlocfilehash: d512a691b76cb7cbc72b4cbcb1fc821e928ea1b0
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421230"
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

1. **Kolumna tekstowa do oczyszczenia** : wybierz kolumnę, którą chcesz wstępnie przetworzyć.

1. **Usuń słowa Stop** : zaznacz tę opcję, jeśli chcesz zastosować wstępnie zdefiniowaną listę odrzucany termin do kolumny tekst. 

    Listy odrzucany termin są zależne od języka i dostosowywalne.

1. **Lematyzacja** : Wybierz tę opcję, jeśli chcesz, aby wyrazy były reprezentowane w postaci kanonicznej. Ta opcja jest przydatna do zmniejszenia liczby unikatowych wystąpień podobnych tokenów tekstowych.

    Proces Lematyzacja jest wysoce zależny od języka...

1. **Wykryj zdania** : zaznacz tę opcję, jeśli chcesz, aby moduł wstawiał znak graniczny zdania podczas przeprowadzania analizy.

    Ten moduł używa serii trzech znaków potoku `|||` do reprezentowania terminatora zdania.

1. Wykonaj opcjonalne operacje znajdowania i zamieniania przy użyciu wyrażeń regularnych.

    * **Niestandardowe wyrażenie regularne** : Zdefiniuj szukany tekst.
    * **Niestandardowy ciąg zamienny** : Zdefiniuj pojedynczą wartość zastępczą.

1. **Normalizing Case do małych liter** : zaznacz tę opcję, jeśli chcesz skonwertować wielką literę ASCII na postać małych liter.

    Jeśli znaki nie są znormalizowane, ten sam wyraz pisany wielkimi literami i małe litery jest uznawany za dwa różne słowa.

1. Można również usunąć następujące typy znaków lub sekwencje znaków z przetworzonego tekstu wyjściowego:

    * **Usuń numery** : zaznacz tę opcję, aby usunąć wszystkie znaki numeryczne dla określonego języka. Numery identyfikacyjne są zależne od domeny i języka. Jeśli znaki numeryczne są integralną częścią znanego wyrazu, numer nie może zostać usunięty.
    
    * **Usuń znaki specjalne** : Użyj tej opcji, aby usunąć wszystkie znaki specjalne inne niż alfanumeryczne.
    
    * **Usuń zduplikowane znaki** : zaznacz tę opcję, aby usunąć dodatkowe znaki w każdej sekwencji powtarzanej przez więcej niż dwa razy. Na przykład sekwencja, taka jak "AAAAA", zostanie zredukowana do "AA".
    
    * **Usuń adresy e-mail** — wybierz tę opcję, aby usunąć dowolną sekwencję formatu `<string>@<string>` .  
    * **Usuń adresy URL** : zaznacz tę opcję, aby usunąć sekwencję zawierającą następujące PREFIKSY adresów URL: `http` , `https` , `ftp` , `www`
    
1. **Rozwiń kontrakty czasownikowe** : Ta opcja ma zastosowanie tylko do języków, które używają umów czasownikowych; obecnie tylko w języku angielskim. 

    Na przykład po wybraniu tej opcji można zastąpić frazę " *nie* Zostań *tam"* ".

1. **Normalizowanie ukośników odwrotnych do ukośników** : Wybierz tę opcję, aby zmapować wszystkie wystąpienia elementu `\\` do `/` .

1. **Podziel tokeny na znaki specjalne** : zaznacz tę opcję, jeśli chcesz przerwać słowa na znakach, takich jak `&` , `-` i tak dalej. Ta opcja może również zmniejszyć liczbę znaków specjalnych, gdy powtarza się więcej niż dwa razy. 

    Na przykład ciąg `MS---WORD` zostałby podzielony na trzy tokeny, `MS` , `-` , i `WORD` .

1. Prześlij potok.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 