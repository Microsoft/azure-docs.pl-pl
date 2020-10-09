---
title: Wyrównanie wyrazów — translator
titleSuffix: Azure Cognitive Services
description: Aby otrzymywać informacje o wyrównaniu, użyj metody tłumaczenia i Dołącz opcjonalny parametr includeAlignment.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7288087bfe7d2a7bb03ce8a99831ad8b7f5b549f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83995619"
---
# <a name="how-to-receive-word-alignment-information"></a>Jak uzyskać informacje dotyczące wyrównania wyrazów

## <a name="receiving-word-alignment-information"></a>Otrzymywanie informacji o wyrównaniu wyrazów
Aby otrzymywać informacje o wyrównaniu, użyj metody tłumaczenia i Dołącz opcjonalny parametr includeAlignment.

## <a name="alignment-information-format"></a>Format informacji o wyrównaniu
Wyrównanie jest zwracane jako wartość ciągu następującego formatu dla każdego wyrazu źródła. Informacje dla każdego wyrazu są oddzielone spacjami, w tym dla języków (skryptów), takich jak chiński:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Przykładowy ciąg wyrównania: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Inaczej mówiąc, dwukropek oddziela indeks początkowy i końcowy, myślnik oddziela Języki, a spacja oddziela słowa. Jedno słowo może być wyrównane z zerem, jednym lub wieloma wyrazami w innym języku, a wyrównane słowa mogą nie być ciągłe. Gdy żadne informacje o wyrównaniu nie są dostępne, element wyrównania będzie pusty. Metoda nie zwraca żadnego błędu w tym przypadku.

## <a name="restrictions"></a>Ograniczenia
Wyrównanie jest zwracane tylko dla podzestawu par języka w tym punkcie:
* od języka angielskiego do dowolnego innego języka;
* z dowolnego innego języka w języku angielskim, z wyjątkiem chiński uproszczony, chiński tradycyjny i łotewski do Polski
* od japońskiego na koreański lub z koreańskiego na japoński nie otrzymasz informacji o wyrównaniu, jeśli zdanie jest przekształceniem w konserwie. Przykładem przeprowadzonego tłumaczenia jest "to jest test", "mam miłość" i inne zdania o wysokiej częstotliwości.

## <a name="example"></a>Przykład

Przykładowy kod JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
