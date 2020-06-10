---
title: Limity usługi w publicznej wersji zapoznawczej
titleSuffix: Azure Digital Twins
description: Wykres przedstawiający limity usługi Azure Digital bliźniaczych reprezentacji w publicznej wersji zapoznawczej.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 4497e1222904b1dad5fbeccd942854443e756ed5
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612845"
---
# <a name="service-limits-in-public-preview"></a>Limity usługi w publicznej wersji zapoznawczej

Są to limity usługi Azure Digital bliźniaczych reprezentacji w publicznej wersji zapoznawczej.

> [!NOTE]
> Niektóre usługi mają regulowane limity, reprezentowane w poniższych tabelach z kolumną z możliwością *dopasowywania?* Aby można było dostosować limit, wartość *ustawiana* to *tak*.
>
> Jeśli firma wymaga podniesienia regulowanego limitu lub limitu przydziału powyżej domyślnego limitu, można zażądać dodatkowych zasobów, [otwierając bilet pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="functional-limits"></a>Limity funkcjonalne

W poniższej tabeli wymieniono funkcjonalne limity usługi Azure Digital bliźniaczych reprezentacji w bieżącej wersji zapoznawczej.

| Obszar | Możliwość | Limit | Wraz? |
| --- | --- | --- | --- |
| Zasób platformy Azure | Liczba wystąpień bliźniaczych reprezentacji cyfrowych platformy Azure w regionie, na subskrypcję | 10 | Tak |
| Digital bliźniaczych reprezentacji | Liczba bliźniaczych reprezentacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji | 200,000 | Tak |
| Routing | Liczba punktów końcowych dla pojedynczego wystąpienia usługi Azure Digital bliźniaczych reprezentacji | 6 | Nie |
| Routing | Liczba tras dla pojedynczego wystąpienia usługi Azure Digital bliźniaczych reprezentacji | 6 | Tak |
| Modele | Liczba modeli w pojedynczym wystąpieniu usługi Azure Digital bliźniaczych reprezentacji | 10 000 | Tak |
| Modele | Liczba modeli, które mogą być przekazane w jednym wywołaniu interfejsu API | 250 | Nie |
| Modele | Liczba elementów zwróconych na jednej stronie | 100 | Nie |
| Zapytanie | Liczba elementów zwróconych na jednej stronie | 100 | Nie |
| Zapytanie | Liczba `AND`  /  `OR` wyrażeń w zapytaniu | 50 | Tak |
| Zapytanie | Liczba elementów tablicy w `IN`  /  `NOT IN` klauzuli | 50 | Tak |
| Zapytanie | Liczba znaków w zapytaniu | 8000 | Tak |
| Zapytanie | Liczba `JOINS` w zapytaniu | 1 | Tak |

## <a name="rate-limits"></a>Limity szybkości

Ta tabela odzwierciedla limity szybkości różnych interfejsów API.

| Interfejs API | Możliwość | Limit | Wraz? |
| --- | --- | --- | --- |
| Interfejs API modeli | Liczba żądań na sekundę | 100 | Tak |
| Interfejs API Digital bliźniaczych reprezentacji | Liczba żądań na sekundę | 1000 | Tak |
| Interfejs API zapytań | Liczba żądań na sekundę | 500 | Tak |
| Interfejs API zapytań | Liczba jednostek zapytania na sekundę | 4000 | Tak |
| Interfejs API tras zdarzeń | Liczba żądań na sekundę | 100 | Tak |

## <a name="other-limits"></a>Inne limity

Limity dotyczące typów danych i pól w DTDL dokumentach dla modeli usługi Azure Digital bliźniaczych reprezentacji można znaleźć w dokumentacji dotyczącej specyfikacji w serwisie GitHub: [Digital bliźniaczych reprezentacji Definition Language (DTDL) — wersja 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Szczegóły opóźnienia zapytania i inne wskazówki dotyczące pisania zapytań w trakcie korzystania z wersji zapoznawczej można znaleźć w temacie [How to: Query The bliźniaczy Graf](how-to-query-graph.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o bieżącej wersji zapoznawczej usługi Azure Digital bliźniaczych reprezentacji w temacie Omówienie usługi:
* [Omówienie: co to jest usługa Azure Digital bliźniaczych reprezentacji?](overview.md)
