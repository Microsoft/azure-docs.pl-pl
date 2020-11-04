---
title: Transakcyjne operacje wsadowe w Azure Cosmos DB przy użyciu zestawu .NET SDK
description: Dowiedz się, jak używać TransactionalBatch w zestawie SDK platformy .NET Azure Cosmos DB, aby wykonać grupę operacji punktu, które kończą się powodzeniem lub niepowodzeniem.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 1f541b947c04619892291e47002ea9b0dbb6d38d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340570"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Transakcyjne operacje wsadowe w Azure Cosmos DB przy użyciu zestawu .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W partii transakcyjnej opisano grupę operacji punktu, które muszą być pomyślne lub niepowodzeniem razem z tym samym kluczem partycji w kontenerze. W zestawie SDK platformy .NET `TranscationalBatch` Klasa jest używana do definiowania tej partii operacji. Jeśli wszystkie operacje zakończą się pomyślnie w kolejności, w której zostały opisane w transakcyjnej operacji wsadowej, transakcja zostanie zatwierdzona. Jeśli jednak jakakolwiek operacja zakończy się niepowodzeniem, cała transakcja zostanie wycofana.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Co to jest transakcja w Azure Cosmos DB

Transakcja w typowej bazie danych może być zdefiniowana jako sekwencja operacji wykonywanych jako pojedyncza jednostka logiczna. Każda transakcja zapewnia gwarancje dotyczące własności KWASowej, spójności, izolacji, trwałości).

* **Niepodzielność** gwarantuje, że wszystkie operacje wykonywane w ramach transakcji są traktowane jako jedna jednostka, a wszystkie z nich są zatwierdzone lub żadna z nich nie jest.
* **Spójność** gwarantuje, że dane są zawsze w prawidłowym stanie między transakcjami.
* **Izolacja** gwarantuje, że żadne dwie transakcje nie zakłócają pracy — wiele systemów komercyjnych zapewnia wiele poziomów izolacji, które mogą być używane w zależności od potrzeb aplikacji.
* **Trwałość** gwarantuje, że wszelkie zmiany, które są zatwierdzone w bazie danych, zawsze będą obecne.
Azure Cosmos DB obsługuje [wszystkie transakcje zgodne ze standardem kwaśnym z izolacją migawki](database-transactions-optimistic-concurrency.md) dla operacji w ramach tego samego [klucza partycji logicznej](partitioning-overview.md).

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Transakcyjne operacje wsadowe a procedury składowane

Azure Cosmos DB obecnie obsługuje procedury składowane, które również zapewniają zakres transakcyjny w operacjach. Jednak transakcyjne operacje wsadowe oferują następujące korzyści:

* **Opcja języka** — partia transakcji jest obsługiwana w zestawie SDK i języku, z którym pracujesz już, podczas gdy procedury składowane muszą być zapisywane w języku JavaScript.
* **Przechowywanie wersji kodu** — przechowywanie wersji kodu aplikacji i dołączanie ich do potoku ciągłej integracji/ciągłego wdrażania jest znacznie bardziej naturalne niż organizowanie aktualizacji procedury składowanej i upewnienie się, że Przerzucanie odbywa się w odpowiednim czasie. Ułatwia również wycofywanie zmian.
* **Wydajność** — skrócenie czasu oczekiwania na równoważne operacje do 30% w porównaniu do wykonywania procedury składowanej.
* **Serializacja zawartości** — każda operacja w ramach transakcyjnej partii może korzystać z opcji serializacji niestandardowej dla ładunku.

## <a name="how-to-create-a-transactional-batch-operation"></a>Jak utworzyć transakcyjną operację wsadową

Podczas tworzenia transakcyjnej operacji wsadowej zaczynasz od wystąpienia kontenera i Wywołaj `CreateTransactionalBatch` :

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Następnie należy wywołać `ExecuteAsync` :

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

Po odebraniu odpowiedzi musisz się dojrzeć, czy jej powodzenie zakończyło się pomyślnie, i Wyodrębnij wyniki:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Jeśli wystąpi awaria, operacja zakończona niepowodzeniem będzie miała kod stanu odpowiedniego błędu. Wszystkie inne operacje będą mieć kod stanu 424 (zależność niepomyślna). W poniższym przykładzie operacja nie powiedzie się, ponieważ próbuje utworzyć element, który już istnieje (409 HttpStatusCode. konflikt). Kody stanu ułatwiają zidentyfikowanie przyczyny błędu transakcji.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Jak są wykonywane transakcyjne operacje wsadowe

Po `ExecuteAsync` wywołaniu metody wszystkie operacje w `TransactionalBatch` obiekcie są grupowane, serializowane do jednego ładunku i wysyłane jako pojedyncze żądanie do usługi Azure Cosmos DB.

Usługa odbiera żądanie i wykonuje wszystkie operacje w zakresie transakcyjnym i zwraca odpowiedź przy użyciu tego samego protokołu serializacji. Ta odpowiedź jest sukcesem lub niepowodzeniem i zawiera wewnętrznie wszystkie odpowiedzi na poszczególne operacje.

Zestaw SDK uwidacznia odpowiedź w celu sprawdzenia wyniku i opcjonalnie Wyodrębnij poszczególne wyniki operacji wewnętrznej.

## <a name="limitations"></a>Ograniczenia

Obecnie istnieją dwa znane limity:

* Limit rozmiaru żądania Azure Cosmos DB określa rozmiar `TransactionalBatch` ładunku nie może przekroczyć 2 MB, a maksymalny czas wykonywania wynosi 5 sekund.
* Istnieje aktualny limit 100 operacji na, `TransactionalBatch` Aby upewnić się, że wydajność jest zgodnie z oczekiwaniami i w umowy SLA.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat tego, co możesz zrobić z TransactionalBatch](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* Zapoznaj się z naszymi [przykładami](sql-api-dotnet-v3sdk-samples.md) , aby dowiedzieć się więcej na temat używania naszego zestawu SDK Cosmos DB .NET
