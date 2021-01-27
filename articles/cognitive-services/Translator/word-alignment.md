---
title: Wyrównanie wyrazów — translator
titleSuffix: Azure Cognitive Services
description: Aby otrzymywać informacje o wyrównaniu, użyj metody tłumaczenia i Dołącz opcjonalny parametr includeAlignment.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 8368ca0ca4c3f2f0ab3cb88a5d54295d71451636
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898023"
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
