---
title: 'Azure Cosmos DB: asynchroniczny interfejs API języka Java SQL, zasoby & zestawu SDK'
description: Dowiedz się więcej o interfejsie API i zestawie SDK asynchronicznego języka SQL, w tym o datach wydania, datach wycofania i zmianach między poszczególnymi wersjami Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: adee8064a97c7757ae7a6d3c9475bc5a3044e50d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232308"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async SDK Java dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [Zestaw SDK .NET w wersji 3](sql-api-sdk-dotnet-standard.md)
> * [Zestaw .NET SDK w wersji 2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK 2](sql-api-sdk-dotnet-core.md)
> * [Zestaw SDK zmiany źródła .NET w wersji 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK 4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * CZĘŚĆ (/rest/api
> * [Dostawca zasobów REST](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET V2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

Pakiet asynchroniczny interfejs Java interfejsu API języka SQL różni się od zestawu SDK Java interfejsu SQL API, zapewniając asynchroniczne operacje z obsługą [biblioteki](https://netty.io/)sieci. Wstępnie istniejący [zestaw SDK Java interfejsu API języka SQL](sql-api-sdk-java.md) nie obsługuje operacji asynchronicznych. 

> [!IMPORTANT]  
> To *nie* jest najnowszy zestaw SDK języka Java dla Azure Cosmos DB! Rozważ użycie [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) dla projektu. Aby przeprowadzić uaktualnienie, postępuj zgodnie z instrukcjami zawartymi w przewodniku [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) i w podręczniku [reaktora vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) . 
>

| |  |
|---|---|
| **Pobieranie zestawu SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Dokumentacja interfejsu API** |[Dokumentacja interfejsu API języka Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Współtworzenie zestawu SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Wprowadzenie** | [Wprowadzenie do asynchronicznego zestawu Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Przykład kodu** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Porady dotyczące wydajności**| [Plik Readme usługi GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimalne obsługiwane środowisko uruchomieniowe**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

