---
author: baanders
description: uwzględnij plik dla Azure Digital Twins limitów
ms.service: digital-twins
ms.topic: include
ms.date: 4/8/2021
ms.author: baanders
ms.openlocfilehash: 34fec713c3764987f07bc7fb89ecb0a0d770a840
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728028"
---
### <a name="functional-limits"></a>Limity funkcjonalne

W poniższej tabeli wymieniono limity funkcjonalności Azure Digital Twins. 

> [!TIP]
> Aby uzyskać zalecenia dotyczące modelowania, które mają działać w ramach tych limitów funkcjonalnych, zobacz [Best practices for designing models](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models)(Najlepsze rozwiązania dotyczące projektowania modeli).

| Warstwowy | Możliwość | Limit domyślny | Regulowane? |
| --- | --- | --- | --- |
| Zasób platformy Azure | Liczba wystąpień Azure Digital Twins w regionie, na subskrypcję | 10 | Tak |
| Digital Twins | Liczba bliźniaczych reprezentacji w Azure Digital Twins wystąpienia | 200,000 | Tak |
| Digital Twins | Liczba relacji przychodzących do pojedynczej bliźniaczej reprezentacji | 5000 | Nie |
| Digital Twins | Liczba relacji wychodzących z pojedynczej bliźniaczej reprezentacji | 5000 | Nie |
| Digital Twins | Maksymalny rozmiar (treści JSON w żądaniu PUT lub PATCH) pojedynczej bliźniaczej reprezentacji | 32 KB | Nie |
| Digital Twins | Maksymalny rozmiar ładunku żądania | 32 KB | Nie | 
| Routing | Liczba punktów końcowych dla pojedynczego Azure Digital Twins wystąpienia | 6 | Nie |
| Routing | Liczba tras dla pojedynczego Azure Digital Twins wystąpienia | 6 | Tak |
| Modele | Liczba modeli w ramach jednego Azure Digital Twins wystąpienia | 10 000 | Tak |
| Modele | Liczba modeli, które można przekazać w jednym wywołaniu interfejsu API | 250 | Nie |
| Modele | Maksymalny rozmiar (treści JSON w żądaniu PUT lub PATCH) pojedynczego modelu | 1 MB | Nie |
| Modele | Liczba elementów zwróconych na jednej stronie | 100 | Nie |
| Zapytanie | Liczba elementów zwróconych na jednej stronie | 100 | Tak |
| Zapytanie | Liczba `AND`  /  `OR` wyrażeń w zapytaniu | 50 | Tak |
| Zapytanie | Liczba elementów tablicy w `IN`  /  `NOT IN` klauzuli | 50 | Tak |
| Zapytanie | Liczba znaków w zapytaniu | 8000 | Tak |
| Zapytanie | Liczba w `JOINS` zapytaniu | 5 | Tak |

### <a name="rate-limits"></a>Limity szybkości

W poniższej tabeli przedstawiono limity szybkości różnych interfejsów API.

| Interfejs API | Możliwość | Limit domyślny | Regulowane? |
| --- | --- | --- | --- |
| Interfejs API modeli | Liczba żądań na sekundę | 100 | Tak |
| Digital Twins API | Liczba żądań odczytu na sekundę | 1000 | Tak |
| Digital Twins API | Liczba żądań poprawek na sekundę | 1000 | Tak |
| Digital Twins API | Liczba operacji tworzenia/usuwania na sekundę we wszystkich **bliźniaczych reprezentacji i relacjach** | 50 | Tak |
| Digital Twins API | Liczba operacji tworzenia/aktualizowania/usuwania na sekundę dla pojedynczej bliźniaczej **reprezentacji** lub jej relacji | 10 | Nie |
| Interfejs API zapytań | Liczba żądań na sekundę | 500 | Tak |
| Interfejs API zapytań | [Jednostki zapytania](../articles/digital-twins/concepts-query-units.md) na sekundę | 4000 | Tak |
| Interfejs API tras zdarzeń | Liczba żądań na sekundę | 100 | Tak |

### <a name="other-limits"></a>Inne limity

Limity typów danych i pól w dokumentach DTDL dla modeli Azure Digital Twins można znaleźć w dokumentacji specyfikacji w witrynie GitHub: [*Digital Twins Definition Language (DTDL) — wersja 2.*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)
 
Szczegóły opóźnień zapytań i inne ograniczenia dotyczące zapytań można znaleźć w te [*tematu: Wykonywanie zapytań dotyczących grafu bliźniaczej reprezentacji.*](../articles/digital-twins/how-to-query-graph.md)
