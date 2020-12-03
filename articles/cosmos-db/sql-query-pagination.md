---
title: Stronicowanie w Azure Cosmos DB
description: Informacje o pojęciach dotyczących stronicowania i tokenach kontynuacji
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: c49fee169b7bd01ee7cf8a6d539c2125cf6568b3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545319"
---
# <a name="pagination-in-azure-cosmos-db"></a>Stronicowanie w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W Azure Cosmos DB kwerendy mogą mieć wiele stron wyników. W tym dokumencie opisano kryteria, które są używane przez aparat zapytań Azure Cosmos DB do podjęcie decyzji, czy dzielić wyniki zapytania na wiele stron. Opcjonalnie można użyć tokenów kontynuacji do zarządzania wynikami zapytania, które obejmują wiele stron.

## <a name="understanding-query-executions"></a>Informacje o wykonywaniu zapytań

Czasami wyniki zapytania zostaną podzielone na wiele stron. Wyniki każdej strony są generowane przez oddzielne wykonywanie zapytań. Gdy nie można zwrócić wyników zapytania w jednym wykonaniu, Azure Cosmos DB automatycznie podzieli wyniki na wiele stron.

Można określić maksymalną liczbę elementów zwracanych przez zapytanie, ustawiając `MaxItemCount` . `MaxItemCount`Jest określony na żądanie i Instruuje aparat zapytań, aby zwrócił tę liczbę elementów lub mniej. Możesz ustawić `MaxItemCount` na, `-1` Jeśli nie chcesz umieszczać limitu liczby wyników na wykonanie zapytania.

Ponadto istnieją inne powody, dla których aparat kwerend może potrzebować podzielenia wyników zapytania na wiele stron. Są one następujące:

- Kontener został ograniczony i nie było dostępne jednostek ru do zwrócenia więcej wyników zapytania
- Odpowiedź wykonania zapytania była zbyt duża
- Czas wykonywania zapytania był zbyt długi
- Aparat zapytań może zwrócić wyniki w dodatkowych wykonaniach, co było wydajniejsze

Liczba elementów zwracanych na wykonanie zapytania będzie zawsze mniejsza lub równa `MaxItemCount` . Jednak inne kryteria mogą mieć ograniczoną liczbę wyników zwracanych przez zapytanie. W przypadku wykonywania tego samego zapytania wiele razy, liczba stron może nie być stała. Na przykład, jeśli zapytanie jest ograniczone, może być mniej dostępne wyniki na stronę, co oznacza, że zapytanie będzie zawierać dodatkowe strony. W niektórych przypadkach istnieje również możliwość zwrócenia pustej strony wyników przez zapytanie.

## <a name="handling-multiple-pages-of-results"></a>Obsługa wielu stron wyników

Aby zapewnić dokładne wyniki zapytania, należy postępować przez wszystkie strony. Należy kontynuować wykonywanie zapytań do momentu braku dodatkowych stron.

Oto kilka przykładów przetwarzania wyników zapytań z wieloma stronami:

- [Zestaw SDK platformy .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Zestaw SDK Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Zestaw SDK dla platformy Node.js](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Zestaw SDK dla języka Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Tokeny kontynuacji

W zestawie .NET SDK i Java SDK można opcjonalnie użyć tokenów kontynuacji jako zakładki dla postępu zapytania. Azure Cosmos DB wykonywania zapytań są bezstanowe po stronie serwera i można je wznowić w dowolnym momencie przy użyciu tokenu kontynuacji. Tokeny kontynuacji nie są obsługiwane w zestawie Node.js SDK. W przypadku zestawu SDK języka Python jest on obsługiwany w przypadku zapytań o pojedynczej partycji, a klucz podstawowy musi być określony w obiekcie Options, ponieważ nie jest wystarczający, aby można było go użyć w zapytaniu.

Oto przykład użycia tokenów kontynuacji:

- [Zestaw SDK platformy .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Zestaw SDK Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Zestaw SDK dla języka Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

Jeśli zapytanie zwraca token kontynuacji, istnieją dodatkowe wyniki zapytania.

W interfejsie API REST Azure Cosmos DB można zarządzać tokenami kontynuacji z `x-ms-continuation` nagłówkiem. Podobnie jak w przypadku zapytań z zestawem SDK .NET lub Java, jeśli `x-ms-continuation` Nagłówek odpowiedzi nie jest pusty, oznacza to, że zapytanie ma dodatkowe wyniki.

O ile korzystasz z tej samej wersji zestawu SDK, tokeny kontynuacji nigdy nie wygasną. Opcjonalnie możesz [ograniczyć rozmiar tokenu kontynuacji](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb). Niezależnie od ilości danych lub liczby partycji fizycznych w kontenerze, zapytania zwracają jeden token kontynuacji.

Nie można używać tokenów kontynuacji dla zapytań z [grupą](sql-query-group-by.md) lub [odrębnie](sql-query-keywords.md#distinct) , ponieważ te zapytania wymagają przechowywania znacznej ilości stanu. W przypadku zapytań z `DISTINCT` , można użyć tokenów kontynuacji w przypadku dodania `ORDER BY` do zapytania.

Oto przykład zapytania `DISTINCT` , które może korzystać z tokenu kontynuacji:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzula ORDER BY](sql-query-order-by.md)
