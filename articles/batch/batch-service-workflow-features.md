---
title: Przepływ pracy i zasoby usługi Batch
description: Dowiedz się więcej o funkcjach usługi Batch i przepływie pracy wysokiego poziomu z punktu widzenia rozwoju.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 76a0b140f3bea4b07a6de632abbcbc3fd26e582d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85965216"
---
# <a name="batch-service-workflow-and-resources"></a>Przepływ pracy i zasoby usługi Batch

W tym przeglądzie najważniejszych składników usługi Azure Batch omówiono przepływ pracy wysokiego poziomu, którego deweloperzy mogą używać do tworzenia równoległych rozwiązań obliczeniowych na dużą skalę wraz z używanymi zasobami usług podstawowych.

Bez względu na to, czy tworzysz rozproszoną aplikację obliczeniową lub usługę, która wystawia bezpośrednie wywołania [interfejsu API REST](/rest/api/batchservice/) , czy używasz innego jednego z [zestawów SDK usługi Batch](batch-apis-tools.md#batch-service-apis), będziesz używać wielu zasobów i funkcji omówionych w tym miejscu.

> [!TIP]
> Bardziej szczegółowe wprowadzenie do usługi Batch można znaleźć w temacie [Podstawy usługi Azure Batch](batch-technical-overview.md). Zobacz też najnowsze [aktualizacje usługi Batch](https://azure.microsoft.com/updates/?product=batch).

## <a name="basic-workflow"></a>Podstawowy przepływ pracy

Poniższy ogólny przepływ pracy to typowy przykład dla niemal wszystkich aplikacji i usług, które korzystają z usługi Batch w przypadku obciążeń przetwarzania równoległego:

1. Przekaż **pliki danych** do przetworzenia na konto usługi [Azure Storage](../storage/index.yml). Usługa Batch oferuje wbudowaną obsługę dostępu do usługi Azure Blob Storage, a podczas wykonywania zadań podrzędnych można pobierać te pliki do [węzłów obliczeniowych](nodes-and-pools.md#nodes).
2. Przekaż **pliki aplikacji**, które będą uruchamiane w ramach zadań podrzędnych. Mogą być to pliki binarne lub skrypty i ich zależności. Są one wykonywane w ramach zadań podrzędnych w zadaniach. W trakcie wykonywania zadań podrzędnych można pobierać te pliki z konta usługi Storage lub używać funkcji [pakietów aplikacji](nodes-and-pools.md#application-packages) usługi Batch na potrzeby zarządzania aplikacjami i ich wdrażania.
3. Utwórz [pulę](nodes-and-pools.md#pools) węzłów obliczeniowych. Podczas tworzenia puli określ liczbę jej węzłów obliczeniowych, ich rozmiar oraz system operacyjny. Każde zadanie podrzędne uruchomione w ramach zadania jest przypisywane do wykonania w jednym z węzłów puli.
4. Utwórz [zadanie](jobs-and-tasks.md#jobs). Zadanie umożliwia zarządzanie kolekcją zadań podrzędnych. Każde zadanie jest kojarzone z określoną pulą, w której będą uruchamiane zadania podrzędne powiązane z tym zadaniem.
5. Dodaj [podzadania](jobs-and-tasks.md#tasks) do zadania. Każde zadanie podrzędne uruchamia aplikację lub skrypt przekazany do przetwarzania plików danych, które są pobierane z konta usługi Storage. Po zakończeniu każdego zadania podrzędnego dane wyjściowe mogą być przekazywane do usługi Azure Storage.
6. Monitoruj postęp zadania i pobieraj dane wyjściowe zadania podrzędnego z usługi Azure Storage.

> [!NOTE]
> Do korzystania z usługi Batch niezbędne jest [konto usługi Batch](accounts.md). Większość rozwiązań usługi Batch używa też skojarzonego konta usługi [Azure Storage](../storage/index.yml) do przechowywania i pobierania plików.

## <a name="batch-service-resources"></a>Zasoby usługi Batch

W poniższych tematach omówiono zasoby partii, które umożliwiają dystrybuowanie scenariuszy obliczeniowych.

- [Konta i konta magazynu zadań wsadowych](accounts.md)
- [Węzły i pule](nodes-and-pools.md)
- [Zadania](jobs-and-tasks.md)
- [Pliki i katalogi](files-and-directories.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
- Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](quick-run-dotnet.md) lub języka [Python](quick-run-python.md). Te przewodniki Szybki start zawierają omówienie przykładowej aplikacji, która korzysta z usługi Batch do wykonywania obciążenia na wielu węzłach obliczeniowych i stosuje usługę Azure Storage do tymczasowego przechowywania i pobierania pliku obciążenia.
- Pobierz i zainstaluj narzędzie [Batch Explorer](https://azure.github.io/BatchExplorer/), aby używać go podczas opracowywania rozwiązań usługi Batch. Użyj narzędzia Batch Explorer do tworzenia, debugowania i monitorowania aplikacji usługi Azure Batch.
- Zobacz zasoby społecznościowe, w tym [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), [repozytorium społeczności partii](https://github.com/Azure/Batch)i [forum Azure Batch](/answers/topics/azure-batch.html).
