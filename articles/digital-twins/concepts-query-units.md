---
title: Jednostki zapytań w usłudze Azure Digital bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: Zrozumienie koncepcji rozliczania jednostek zapytań w usłudze Azure Digital bliźniaczych reprezentacji
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 5ba765f03e7ac700fb4338e14358fc55e6423c47
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097043"
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
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wysyłaniu zapytań do usługi Azure Digital bliźniaczych reprezentacji, odwiedź stronę:
* [*Koncepcje: język zapytań*](concepts-query-language.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](how-to-query-graph.md)
* [Dokumentacja interfejsu API zapytań](/rest/api/digital-twins/dataplane/query/querytwins)

Limity dotyczące zapytań Digital bliźniaczych reprezentacji systemu Azure można znaleźć w temacie [*Reference: limit usług*](reference-service-limits.md).