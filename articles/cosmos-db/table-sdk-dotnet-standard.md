---
title: Azure Cosmos DB interfejs API tabel .NET Standard SDK & zasobów
description: Zapoznaj się ze wszystkimi Azure Cosmos DB interfejs API tabel i zestawem SDK .NET Standard, w tym datami wydania, datami wycofania i zmianami wprowadzonymi między każdą wersją.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: bf8563274d7aa677249335612d0156d6a5ecbd95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018454"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB tabelę .NET Standard interfejs API: pobieranie i informacje o wersji
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Pobieranie zestawu SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Przykład**|[Przykład Cosmos DB interfejs API tabel .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Szybki start**|[Szybki start](create-table-dotnet.md)|
|**Samouczek**|[Samouczek](tutorial-develop-table-dotnet.md)|
|**Bieżąca obsługiwana platforma**|[Microsoft .NET Standard 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Zgłoś problem**|[Zgłoś problem](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Informacje o wersji dla serii 2.0.0
Seria 2.0.0 jest zależna od [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), a ulepszenia wydajności i konsolidacja przestrzeni nazw do punktu końcowego Cosmos DB.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0 — wersja zapoznawcza
* wstępna wersja zapoznawcza zestawu SDK tabeli 2.0.0, która jest zależna od elementu [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)z ulepszeniami wydajności i konsolidacją przestrzeni nazw do Cosmos DBego punktu końcowego. Publiczny interfejs API pozostaje taki sam.

## <a name="release-notes-for-100-series"></a>Informacje o wersji dla serii 1.0.0
Seria 1.0.0 przyjmuje zależność od [Microsoft.Azure.DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="108"></a><a name="1.0.8"></a>1.0.8
* Dodaj obsługę, aby ustawić właściwość TTL, jeśli jest to punkt końcowy cosmosdb 
* Zahonorowanie zasad ponawiania próby po upływie limitu czasu i anulowania zadania
* Rozwiązano wyjątek nieprzerwanego anulowania zadania w aplikacjach ASP .NET
* Napraw pobieranie z usługi Azure Table Storage z pomocniczego punktu końcowego tryb lokalizacji
* Zaktualizuj `Microsoft.Azure.DocumentDB.Core` wersję zależności do 2.11.2, która rozwiązuje tymczasowy wyjątek odwołania o wartości null
* Zaktualizuj `Odata.Core` wersję zależności do 7.6.4, która rozwiązuje konflikt zgodności z usługą Azure Shell

### <a name="107"></a><a name="1.0.7"></a>1.0.7
* Zwiększenie wydajności przez ustawienie domyślnego poziomu śledzenia zestawu SDK tabeli na SourceLevels. off, który można włączyć za pomocą app.config

### <a name="105"></a><a name="1.0.5"></a>1.0.5
* Wprowadź nową konfigurację w obszarze TableClientConfiguration, aby używać modułu wykonawczego REST do komunikowania się z Cosmos DB interfejs API tabel

### <a name="105-preview"></a><a name="1.0.5-preview"></a>1.0.5 — wersja zapoznawcza
* Poprawki błędów

### <a name="104"></a><a name="1.0.4"></a>1.0.4
* Poprawki błędów
* Podaj opcję HttpClientTimeout dla RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"></a>1.0.4 — wersja zapoznawcza
* Poprawki błędów
* Podaj opcję HttpClientTimeout dla RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Poprawki błędów

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Wersja ogólna dostępności

### <a name="0110-preview"></a><a name="0.11.0-preview"></a>0.11.0 — wersja zapoznawcza
* Wprowadzono zmiany w sposobie konfigurowania CloudTableClient. Teraz pobiera obiekt TableClientConfiguration w trakcie konstruowania. TableClientConfiguration udostępnia różne właściwości do konfigurowania zachowania klienta w zależności od tego, czy docelowy punkt końcowy jest Cosmos DB interfejs API tabel czy interfejs API tabel usługi Azure Storage.
* Dodano obsługę do TableQuery w celu zwrócenia wyników w kolejności posortowanej w kolumnie niestandardowej. Ta funkcja jest obsługiwana tylko dla punktów końcowych tabeli Cosmos DB.
* Dodano obsługę w celu udostępnienia RequestCharges na różnych typach wyników. Ta funkcja jest obsługiwana tylko dla punktów końcowych tabeli Cosmos DB.

### <a name="0101-preview"></a><a name="0.10.1-preview"></a>0.10.1 — wersja zapoznawcza
* Dodano obsługę tokenów SAS, operacji TablePermissions, serviceproperties i servicestatystyk względem punktów końcowych tabeli usługi Azure Storage. 
   > [!NOTE]
   > Niektóre funkcje w poprzednich zestawach SDK tabeli usługi Azure Storage nie są jeszcze obsługiwane, takie jak szyfrowanie po stronie klienta.

### <a name="0100-preview"></a><a name="0.10.0-preview"></a>0.10.0 — wersja zapoznawcza
* Dodawanie obsługi podstawowych operacji CRUD, Batch i zapytań do punktów końcowych tabeli usługi Azure Storage. 
   > [!NOTE]
   > Niektóre funkcje w poprzednich zestawach SDK tabeli usługi Azure Storage nie są jeszcze obsługiwane, takie jak szyfrowanie po stronie klienta.

### <a name="091-preview"></a><a name="0.9.1-preview"></a>od 0.9.1 — wersja zapoznawcza
* Azure Cosmos DB Table .NET Standard SDK to międzyplatformowa biblioteka platformy .NET, która zapewnia wydajny dostęp do modelu danych tabeli w Cosmos DB. Ta wersja wstępna obsługuje pełny zestaw funkcji zapytania CRUD + Entity z podobnymi interfejsami API jako [zestaw SDK tabeli Cosmos DB dla .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Punkty końcowe tabeli usługi Azure Storage nie są jeszcze obsługiwane w wersji od 0.9.1-Preview.

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Ta międzyplatformowa biblioteka .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) zastąpi bibliotekę .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>Seria 2.0.0
| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [2.0.0 — wersja zapoznawcza](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>Seria 1.0.0
| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 września 2019 |--- |
| [1.0.5 — wersja zapoznawcza](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4 — wersja zapoznawcza](#1.0.4-preview) |26 lipca 2019 |--- |
| 1.0.2 — wersja zapoznawcza |2 maja 2019 |--- |
| [1.0.1](#1.0.1) |19 kwietnia 2019 |--- |
| [1.0.0](#1.0.0) |13 marca 2019 |--- |
| [0.11.0 — wersja zapoznawcza](#0.11.0-preview) |5 marca 2019 |--- |
| [0.10.1 — wersja zapoznawcza](#0.10.1-preview) |22 stycznia 2019 r. |--- |
| [0.10.0 — wersja zapoznawcza](#0.10.0-preview) |18 grudnia, 2018 |--- |
| [od 0.9.1 — wersja zapoznawcza](#0.9.1-preview) |18 października 2018 |--- |


## <a name="faq"></a>Najczęściej zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat interfejs API tabel Azure Cosmos DB, zobacz [wprowadzenie do Azure Cosmos DB interfejs API tabel](table-introduction.md).
