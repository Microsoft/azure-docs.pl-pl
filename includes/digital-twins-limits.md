---
author: baanders
description: plik dołączany dla limitów cyfrowych bliźniaczych reprezentacji na platformie Azure
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 4a69b2ff15fc4857e9fb292d2f753aa68ed875d4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370113"
---
### <a name="functional-limits"></a>Limity funkcjonalne

Poniższa tabela zawiera listę limitów funkcjonalnych usługi Azure Digital bliźniaczych reprezentacji. 

> [!TIP]
> Aby uzyskać zalecenia dotyczące modelowania do działania w ramach tych limitów funkcjonalności, zobacz [najlepsze rozwiązania dotyczące projektowania modeli](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models).

| Warstwowy | Możliwość | Limit domyślny | Wraz? |
| --- | --- | --- | --- |
| Zasób platformy Azure | Liczba wystąpień bliźniaczych reprezentacji cyfrowych platformy Azure w regionie, na subskrypcję | 10 | Tak |
| Digital Twins | Liczba bliźniaczych reprezentacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji | 200,000 | Tak |
| Digital Twins | Liczba przychodzących relacji do pojedynczej sznurka | 5000 | Nie |
| Digital Twins | Liczba wychodzących relacji z pojedynczej sznurka | 5000 | Nie |
| Digital Twins | Maksymalny rozmiar (treść w formacie JSON w żądaniu PUT lub PATCH) pojedynczej bliźniaczyej | 32 KB | Nie |
| Digital Twins | Maksymalny rozmiar ładunku żądania | 32 KB | Nie | 
| Routing | Liczba punktów końcowych dla pojedynczego wystąpienia usługi Azure Digital bliźniaczych reprezentacji | 6 | Nie |
| Routing | Liczba tras dla pojedynczego wystąpienia usługi Azure Digital bliźniaczych reprezentacji | 6 | Tak |
| Modele | Liczba modeli w pojedynczym wystąpieniu usługi Azure Digital bliźniaczych reprezentacji | 10 000 | Tak |
| Modele | Liczba modeli, które mogą być przekazane w jednym wywołaniu interfejsu API | 250 | Nie |
| Modele | Maksymalny rozmiar (treść w formacie JSON w żądaniu PUT lub PATCH) jednego modelu | 1 MB | Nie |
| Modele | Liczba elementów zwróconych na jednej stronie | 100 | Nie |
| Zapytanie | Liczba elementów zwróconych na jednej stronie | 100 | Tak |
| Zapytanie | Liczba `AND`  /  `OR` wyrażeń w zapytaniu | 50 | Tak |
| Zapytanie | Liczba elementów tablicy w `IN`  /  `NOT IN` klauzuli | 50 | Tak |
| Zapytanie | Liczba znaków w zapytaniu | 8000 | Tak |
| Zapytanie | Liczba `JOINS` w zapytaniu | 5 | Tak |

### <a name="rate-limits"></a>Limity szybkości

W poniższej tabeli przedstawiono limity szybkości różnych interfejsów API.

| Interfejs API | Możliwość | Limit domyślny | Wraz? |
| --- | --- | --- | --- |
| Interfejs API modeli | Liczba żądań na sekundę | 100 | Tak |
| Interfejs API Digital bliźniaczych reprezentacji | Liczba żądań na sekundę | 2000 | Tak |
| Interfejs API Digital bliźniaczych reprezentacji | Liczba operacji tworzenia/usuwania na sekundę dla **wszystkich bliźniaczych reprezentacji i relacji** | 50 | Tak |
| Interfejs API Digital bliźniaczych reprezentacji | Liczba operacji tworzenia/aktualizowania/usuwania na sekundę na **jednej dwuosiowej** lub jej relacji | 10 | Nie |
| Interfejs API zapytań | Liczba żądań na sekundę | 500 | Tak |
| Interfejs API zapytań | Liczba [jednostek zapytania](../articles/digital-twins/concepts-query-units.md) na sekundę | 4000 | Tak |
| Interfejs API tras zdarzeń | Liczba żądań na sekundę | 100 | Tak |

### <a name="other-limits"></a>Inne limity

Limity dotyczące typów danych i pól w DTDL dokumentach dla modeli usługi Azure Digital bliźniaczych reprezentacji można znaleźć w dokumentacji dotyczącej specyfikacji w serwisie GitHub: [*Digital bliźniaczych reprezentacji Definition Language (DTDL) — wersja 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Szczegóły opóźnienia zapytania i inne ograniczenia zapytania można znaleźć w temacie [*How to: Query The bliźniaczy Graf*](../articles/digital-twins/how-to-query-graph.md).
