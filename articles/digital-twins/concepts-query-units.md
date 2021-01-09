---
title: Jednostki zapytań w usłudze Azure Digital bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: Zrozumienie koncepcji rozliczania jednostek zapytań w usłudze Azure Digital bliźniaczych reprezentacji
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 86f2abb8bfb95d5b9e72936ca3e9464747c00b1c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049307"
---
# <a name="query-units-in-azure-digital-twins"></a>Jednostki zapytań w usłudze Azure Digital bliźniaczych reprezentacji 

Usługa Azure Digital bliźniaczych reprezentacji **Query Unit (Qu)** jest jednostką obliczeń na żądanie, która służy do wykonywania [zapytań dotyczących cyfrowych bliźniaczych reprezentacji na platformie Azure](how-to-query-graph.md) przy użyciu [interfejsu API zapytań](/rest/api/digital-twins/dataplane/query). 

Umożliwia ona poddzielenie zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonywania operacji zapytania obsługiwane przez usługę Azure Digital bliźniaczych reprezentacji, co pozwala na śledzenie użycia w jednostkach zapytań.

Liczba jednostek zapytania zużytych do wykonania zapytania jest zależna od...

* złożoność zapytania
* rozmiar zestawu wyników (dlatego zapytanie zwracające 10 wyników będzie zużywać więcej QUs niż zapytanie o podobną złożoność zwracającą tylko jeden wynik)

W tym artykule wyjaśniono, jak zrozumieć jednostki zapytań i śledzić użycie jednostek zapytań.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Znajdowanie użycia jednostki zapytania w usłudze Azure Digital bliźniaczych reprezentacji

Po uruchomieniu zapytania przy użyciu [interfejsu API](/rest/api/digital-twins/dataplane/query)Digital bliźniaczych reprezentacji zapytania platformy Azure można sprawdzić nagłówek odpowiedzi, aby śledzić liczbę QUs użytych przez zapytanie. Poszukaj "opłaty za zapytanie" w odpowiedzi wysyłanej z powrotem z usługi Azure Digital bliźniaczych reprezentacji.

Usługi Azure Digital bliźniaczych reprezentacji [SDK](how-to-use-apis-sdks.md) umożliwiają wyodrębnienie nagłówka opłaty zapytania z odpowiedzi stronicowanej. W tej sekcji pokazano, jak wykonywać zapytania dotyczące cyfrowego bliźniaczych reprezentacji oraz jak wykonać iterację odpowiedzi stronicowanej w celu wyodrębnienia nagłówka z opłatą za zapytanie. 

Poniższy fragment kodu demonstruje, jak można wyodrębnić opłaty za zapytania podczas wywoływania interfejsu API zapytań. Najpierw wykonuje iterację na stronach odpowiedzi, aby uzyskać dostęp do nagłówka opłaty zapytania, a następnie wykonuje iterację przez cyfrowe sznurki na każdej stronie. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wysyłaniu zapytań do usługi Azure Digital bliźniaczych reprezentacji, odwiedź stronę:

* [*Koncepcje: język zapytań*](concepts-query-language.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](how-to-query-graph.md)
* [Dokumentacja interfejsu API zapytań](/rest/api/digital-twins/dataplane/query/querytwins)

Limity dotyczące zapytań Digital bliźniaczych reprezentacji systemu Azure można znaleźć w temacie [*Reference: limit usług*](reference-service-limits.md).