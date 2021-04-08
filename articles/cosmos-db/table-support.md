---
title: Pomoc techniczna usługi Azure Table Storage w usłudze Azure Cosmos DB
description: Dowiedz się, jak Azure Cosmos DB interfejs API tabel i tabele usługi Azure Storage współpracują z tym samym modelem danych tabeli i operacjami
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 01/08/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: c2b2a9a03a654e8ccba58e62a1d017cd11db5abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050770"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Programowanie za pomocą interfejsu API tabel usługi Azure Cosmos DB oraz usługi Azure Table Storage
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Interfejs API tabel usługi Azure Cosmos DB oraz usługa Azure Table Storage mają ten sam model danych tabeli oraz udostępniają te same operacje tworzenia, usuwania, aktualizacji i zapytań w swoich zestawach SDK.

> [!NOTE]
> [Tryb wydajności bezserwerowej](serverless.md) jest teraz dostępny w interfejs API tabel Azure Cosmos DB.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Programowanie z użyciem interfejsu API tabel usługi Azure Cosmos DB

W tej chwili [interfejs API tabel usługi Azure Cosmos DB](table-introduction.md) ma cztery zestawy SDK dostępne do programowania: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Elementem docelowym tej biblioteki jest platforma .NET Standard i ma ona takie same klasy i podpisy metod jak publiczny [zestaw SDK usługi Windows Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage), ale ma również możliwość łączenia się z kontami usługi Azure Cosmos DB przy użyciu interfejsu API tabel. Użytkownicy biblioteki .NET Framework Library [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) są zalecani do uaktualnienia do [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , ponieważ jest w trybie konserwacji i wkrótce zostaną zaniechane.

* [Zestaw SDK języka Python](table-sdk-python.md): nowy Azure Cosmos DB Python SDK jest jedynym zestawem SDK, który obsługuje usługę Azure Table Storage w języku Python. Ten zestaw SDK łączy się z usługą Azure Table Storage oraz interfejsem API tabel usługi Azure Cosmos DB.

* [Zestaw Java SDK](table-sdk-java.md): ten zestaw SDK usługi Azure Storage umożliwia łączenie się z kontami Azure Cosmos dB przy użyciu interfejs API tabel.

* [Node.js SDK](table-sdk-nodejs.md): ten zestaw SDK usługi Azure Storage umożliwia łączenie się z kontami Azure Cosmos dB przy użyciu interfejs API tabel.


Dodatkowe informacje dotyczące pracy z interfejsem API tabel są dostępne w artykule: [Często zadawane pytania: programowanie przy użyciu interfejsu API tabel](table-api-faq.md).

## <a name="developing-with-azure-table-storage"></a>Programowanie z wykorzystaniem usługi Azure Table Storage

Magazyn tabel Azure oferuje do programowania następujące zestawy SDK:

- Biblioteka [Microsoft. Azure. Storage. blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), [Microsoft. Azure. Storage. File](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/), [Microsoft. Azure. Storage. Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)i [Microsoft. Azure. Storage. Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) librarys umożliwia korzystanie z usługi Azure Table Storage. Jeśli używasz interfejs API tabel w Azure Cosmos DB, możesz zamiast tego użyć biblioteki [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) .
- [Zestaw SDK języka Python](https://github.com/Azure/azure-cosmos-table-python). Zestaw SDK tabeli Azure Cosmos DB dla języka Python obsługuje usługę Table Storage (ponieważ Table Storage platformy Azure i interfejs API tabel Cosmos DB współdzielą te same funkcje i możliwości, a w celu factorize naszych wysiłków związanych z programowaniem zestawów SDK zalecamy użycie tego zestawu SDK).
- [Zestaw SDK usługi Azure Storage dla języka Java](https://github.com/azure/azure-storage-java). Ten zestaw SDK usługi Azure Storage zapewnia bibliotekę klienta w środowisku Java do pracy z usługą Azure Table Storage.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Zestaw SDK zawiera pakiet Node.js i bibliotekę klienta JavaScript zgodną z przeglądarką, aby korzystać z usługi Table Storage.
- [Moduł programu PowerShell AzureRmStorageTable ](https://www.powershellgallery.com/packages/AzureRmStorageTable). Ten moduł programu PowerShell zawiera polecenia cmdlet do pracy z tabelami usługi Storage.
- [Biblioteka klienta usługi Azure Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/). Ta biblioteka umożliwia tworzenie aplikacji w usłudze Azure Storage.
- [Biblioteka klienta tabel usługi Azure Storage dla Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Ten projekt zapewnia pakiet Ruby, który ułatwia dostęp do tabel usługi Azure Storage.
- [Biblioteka klienta PHP tabel usługi Azure Storage](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ten projekt zapewnia bibliotekę klienta PHP, która ułatwia dostęp do tabel usługi Azure Storage.


   





