---
title: Wprowadzenie do interfejsu API tabel usługi Azure Cosmos DB
description: Dowiedz się, jak korzystać z usługi Azure Cosmos DB i interfejsu API tabel platformy Azure w celu przechowywania ogromnych wolumenów danych typu klucz-wartość i wykonywania zapytań na tych danych przy zachowaniu małych opóźnień.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: 9f44279e47de737bd23fcd0266b7fb73e6245b13
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096057"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Wprowadzenie do usługi Azure Cosmos DB: Interfejs API tabel
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Usługa [Azure Cosmos DB](introduction.md) zapewnia interfejs API tabel dla aplikacji korzystających z usługi Azure Table Storage, które wymagają funkcji warstwy Premium, takich jak:

* [Gotowe globalna](distribute-data-globally.md).
* [Dedykowana przepływność](partitioning-overview.md) na całym świecie.
* Opóźnienie rzędu kilku milisekund na poziomie 99. percentyla.
* Gwarantowana wysoka dostępność.
* Automatyczne indeksowanie pomocnicze.

Aplikacje korzystające z usługi Azure Table Storage mogą być migrowane do usługi Azure Cosmos DB przy użyciu interfejsu API tabel bez zmian kodu i mogą korzystać z funkcji warstwy Premium. Interfejs API tabel ma zestawy SDK klienta dostępne dla platform .NET, Java, Python i Node.js.

> [!IMPORTANT]
> Zestaw .NET Framework SDK [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) jest w trybie konserwacji i zostanie wkrótce wycofany. Wykonaj uaktualnienie do nowej biblioteki .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , aby kontynuować pobieranie najnowszych funkcji obsługiwanych przez interfejs API tabel.

## <a name="table-offerings"></a>Oferty dotyczące tabel
Jeśli obecnie używasz usługi Azure Table Storage, po rozpoczęciu korzystania z interfejsu API tabel usługi Azure Cosmos DB uzyskasz następujące korzyści:

| Cechy | Azure Table Storage | Interfejs API tabel usługi Azure Cosmos DB |
| --- | --- | --- |
| Opóźnienie | Niewielkie, ale brak górnych granic opóźnienia. | Opóźnienie jednocyfrowej milisekundy dla operacji odczytu i zapisu, których kopia zapasowa ma <10 ms (opóźnienie) do odczytu i zapisu w 99 percentylu, w dowolnej skali, w dowolnym miejscu na świecie. |
| Przepływność | Zmienny model przepływności. Tabele mają limit skalowalności 20 000 operacji/s. | Wysoka skalowalność dzięki [dedykowanej zarezerwowanej przepływności na tabelę](request-units.md), gwarantowanej umowami SLA. Konta nie mają górnego limitu przepływności i obsługują >10 milionów operacji/s na tabelę. |
| Dystrybucja globalna | Jeden region z jednym opcjonalnym dodatkowym regionem odczytu, co zapewnia wysoką dostępność. | [Gotowe globalną dystrybucję](distribute-data-globally.md) od jednej do kilku regionów. Obsługa [automatycznego i ręcznego trybu failover](high-availability.md) w dowolnym momencie i każdym miejscu na świecie. Wiele regionów zapisu, aby umożliwić każdemu regionowi akceptowanie operacji zapisu. |
| Indeksowanie | Tylko podstawowy indeks PartitionKey i RowKey. Brak dodatkowych indeksów. | Automatyczne i kompletne indeksowanie wszystkich właściwości, bez zarządzania indeksem. |
| Zapytanie | Wykonanie zapytania wykorzystuje indeks klucza podstawowego, a w przeciwnym przypadku skanuje. | Zapytania mogą korzystać z automatycznego indeksowania właściwości, co skraca czas odpowiedzi. |
| Spójność | Na poziomie „strong” w regionie podstawowym, na poziomie „eventual” w regionie pomocniczym. | [Pięć dobrze zdefiniowanych poziomów spójności](consistency-levels.md) , aby zapewnić dostępność, opóźnienia, przepływność i spójność w zależności od potrzeb aplikacji. |
| Cennik | Optymalizacja pod kątem magazynu. | Optymalizacja pod kątem przepływności. |
| Umowy SLA | 99,9% do 99,99% dostępności, w zależności od strategii replikacji. | 99,999% dostępności odczytu, 99,99% dostępności zapisu na koncie w jednym regionie i 99,999% o dostępności na kontach wieloregionowych. [Kompleksowe umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) obejmujące dostępność, opóźnienia, przepływność i spójność. |

## <a name="get-started"></a>Rozpoczęcie pracy

Utwórz konto usługi Azure Cosmos DB w witrynie [Azure Portal](https://portal.azure.com). Następnie zapoznaj się z naszym [przewodnikiem Szybki Start dotyczącym korzystania z interfejsu API tabel przy użyciu programu .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Jeśli utworzono konto interfejsu API tabel w trakcie okresu próbnego, utwórz [nowe konto interfejsu API tabel](create-table-dotnet.md#create-a-database-account), aby móc korzystać z ogólnie dostępnych zestawów SDK interfejsu API tabel.
>

## <a name="next-steps"></a>Następne kroki

Oto kilka wskazówek ułatwiających rozpoczęcie pracy:
* [Tworzenie aplikacji .NET za pomocą interfejsu API tabel](create-table-dotnet.md)
* [Opracowywanie zawartości przy użyciu interfejsu API tabel na platformie .NET](tutorial-develop-table-dotnet.md)
* [Wykonywanie zapytań dotyczących danych tabel przy użyciu interfejsu API tabel](tutorial-query-table.md)
* [Dowiedz się, jak skonfigurować dystrybucję globalną usługi Azure Cosmos DB przy użyciu interfejsu API tabel](tutorial-global-distribution-table.md)
* [Zestaw SDK tabeli Azure Cosmos DB .NET Standard](table-sdk-dotnet-standard.md)
* [Zestaw SDK tabeli Azure Cosmos DB dla platformy .NET](table-sdk-dotnet.md)
* [Zestaw SDK języka Java dla tabeli Azure Cosmos DB](table-sdk-java.md)
* [Zestaw SDK tabeli Azure Cosmos DB Node.js](table-sdk-nodejs.md)
* [Azure Cosmos DB — zestaw SDK tabel dla języka Python](table-sdk-python.md)
