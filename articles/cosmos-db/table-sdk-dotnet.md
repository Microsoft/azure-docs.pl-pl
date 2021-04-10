---
title: Azure Cosmos DB interfejs API tabel zestawu .NET SDK & zasobów
description: Dowiedz się więcej na temat interfejs API tabel Azure Cosmos DB dla platformy .NET, w tym daty wydania, daty wycofania i zmiany dokonane między poszczególnymi wersjami.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.custom: devx-track-dotnet
ms.openlocfilehash: 5af77f34f8e82edf187c1a68b61995ec5c82a8d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566833"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Interfejs API platformy .NET tabeli Azure Cosmos DB: pobieranie i informacje o wersji
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Linki|
|---|---|
|**Zestaw SDK do pobrania**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Szybki start**|[Azure Cosmos DB: kompilowanie aplikacji za pomocą platformy .NET i interfejs API tabel](create-table-dotnet.md)|
|**Samouczek**|[Azure Cosmos DB: programowanie za pomocą interfejsu Table API na platformie .NET](tutorial-develop-table-dotnet.md)|
|**Bieżąca obsługiwana platforma**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Zestaw .NET Framework SDK [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) jest w trybie konserwacji i zostanie wkrótce wycofany. Wykonaj uaktualnienie do nowej biblioteki .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , aby kontynuować pobieranie najnowszych funkcji obsługiwanych przez interfejs API tabel.

> Jeśli utworzono konto interfejsu API tabel w trakcie okresu próbnego, utwórz [nowe konto interfejsu API tabel](create-table-dotnet.md#create-a-database-account), aby móc korzystać z ogólnie dostępnych zestawów SDK interfejsu API tabel.
>

## <a name="release-notes"></a>Informacje o wersji

### <a name="212"></a><a name="2.1.2"></a>2.1.2

* Poprawki błędów

### <a name="210"></a><a name="2.1.0"></a>2.1.0

* Poprawki błędów

### <a name="200"></a><a name="2.0.0"></a>2.0.0

* Dodano obsługę zapisu dla wieloregionu
* Stałe zależności pakietów NuGet na Microsoft.Azure.DocumentDB, Microsoft. OData. Core, Microsoft. OData. EDM, Microsoft. przestrzenny

### <a name="113"></a><a name="1.1.3"></a>1.1.3

* Stałe zależności pakietów NuGet w witrynie Microsoft. Azure. Storage. Common i Microsoft.Azure.DocumentDB.
* Poprawki podczas serializacji tabeli podczas konfigurowania JsonConvert. DefaultSettings.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Dodano weryfikację dla nieprawidłowo sformułowanych elementów ETAG w trybie bezpośrednim.
* Stała usterka zapytania LINQ w trybie bramy.
* Synchroniczne interfejsy API działają teraz w puli wątków z SynchronizationContext.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Dodaj TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism i TableQueryContinuationTokenLimitInKb do TableRequestOptions
* Poprawki błędów

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Wersja ogólna dostępności

### <a name="090-preview"></a><a name="0.1.0-preview"></a>0.9.0 — wersja zapoznawcza

* Początkowa wersja zapoznawcza

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania

Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

`Microsoft.Azure.CosmosDB.Table`Biblioteka jest obecnie dostępna tylko dla .NET Framework i jest w trybie konserwacji i zostanie wkrótce wycofana. Nowe funkcje i możliwości i optymalizacje są dodawane tylko do biblioteki .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). w związku z tym zaleca się uaktualnienie do [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

Pakiet [windowsazure. Storage-Premium](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) w wersji zapoznawczej został uznany za przestarzały. Zestaw SDK WindowsAzure. Storage-Premium zostanie wycofany 15 listopada 2018, gdy żądania do wycofanego zestawu SDK nie będą dozwolone.

| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16 września 2019| |
| [2.1.0](#2.1.0) |22 stycznia 2019 r.|01 kwietnia 2020 |
| [2.0.0](#2.0.0) |26 września 2018|01, 2020 |
| [1.1.3](#1.1.3) |17 lipca 2018 r.|01, 2019 |
| [1.1.1](#1.1.1) |26 marca 2018|01, 2019 |
| [1.1.0](#1.1.0) |21 lutego 2018|01, 2019 |
| [1.0.0](#1.0.0) |15 listopada 2017 r.|15 listopada 2019 |
| 0.9.0 — wersja zapoznawcza |11 listopada 2017 |11 listopada 2019 |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi błąd 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

podczas próby użycia pakietu NuGet Microsoft. Azure. CosmosDB. Table dostępne są dwie opcje rozwiązania problemu:

* Użyj konsoli Zarządzanie pakietami, aby zainstalować pakiet Microsoft. Azure. CosmosDB. Table wraz z jego zależnościami. W tym celu wpisz następujące polecenie w konsoli Menedżera pakietów dla Twojego rozwiązania. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Przed zainstalowaniem Microsoft. Azure. CosmosDB. Table przy użyciu preferowanego narzędzia do zarządzania pakietami NuGet Zainstaluj pakiet NuGet Microsoft. Azure. Storage. Common.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też

Aby dowiedzieć się więcej na temat interfejs API tabel Azure Cosmos DB, zobacz [wprowadzenie do Azure Cosmos DB interfejs API tabel](table-introduction.md). 
