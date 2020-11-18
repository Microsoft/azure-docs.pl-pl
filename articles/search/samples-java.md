---
title: Przykłady w języku Java
titleSuffix: Azure Cognitive Search
description: Znajdź przykłady kodu w języku Java dla platformy Azure Wyszukiwanie poznawcze, które używają zestawu Azure .NET SDK dla języka Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 26e30b42906a3d8d7a3fcdc013537104a85f32fe
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701861"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Przykłady kodu Java dla platformy Azure Wyszukiwanie poznawcze

Zapoznaj się z przykładami kodu Java, które demonstrują funkcje i funkcje Wyszukiwanie poznawcze platformy Azure. Główne repozytoria są następujące:

| Repozytorium | Opis |
|------------|-------------|
| [Azure-SDK — for Java/Tree/Master/SDK/Search/Azure-Search-Documents/src/Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Przykłady wytwarzane przez zespół zestawu Azure SDK, który jest dostarczany z Azure.Search.Docą uments klienta w zestawie SDK. Możesz również przejrzeć [testy jednostkowe](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) dla biblioteki klienta, aby zobaczyć, jak są wywoływane różne interfejsy API. |
| [Azure-Samples/Azure-Search-Java-Samples](https://github.com/Azure-Samples/azure-search-java-samples) | Przykłady kodu, które towarzyszą artykułom z artykułu. **Przykłady w tym repozytorium nie zostały jeszcze zaktualizowane do korzystania z zestawu Azure SDK dla języka Java**. Obecnie te przykłady wywołują interfejsy API REST w kodzie Java.|

> [!Tip]
> Wypróbuj [przeglądarkę przykładów](/samples/browse/?languages=csharp&products=azure-cognitive-search) , aby wyszukać przykłady kodu firmy Microsoft w witrynie GitHub, filtrować według produktu, usługi i języka.

## <a name="java-sdk-samples"></a>Przykłady zestawu SDK języka Java

Zestaw Azure SDK dla języka Java zawiera wiele przykładów i [stronę wprowadzającą](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) obejmującą instalację pakietu. Na stronie znajduje się również lista wielu przykładów. Poniżej przedstawiono kilka typowych operacji dla wygody użytkownika.

| Samples | Opis |
|---------|-------------|
| [Tworzenie indeksu wyszukiwania](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Demonstruje sposób tworzenia [indeksów wyszukiwania](search-what-is-an-index.md). |
| [Tworzenie synonimu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Pokazuje, jak tworzyć [mapy synonimów](search-synonyms.md).  |
| [Tworzenie indeksatora wyszukiwania](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Pokazuje, jak tworzyć [indeksatory](search-indexer-overview.md). |
| [Tworzenie źródła danych indeksatora wyszukiwania](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Pokazuje, jak tworzyć źródła danych indeksatora wymagane dla indeksowania opartego na indeksatorze dla [obsługiwanych źródeł danych platformy Azure](search-indexer-overview.md#supported-data-sources). |
| [Tworzenie zestawu umiejętności](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Pokazuje, w jaki sposób tworzyć [umiejętności](cognitive-search-working-with-skillsets.md) dołączone indeksatory, które wykonują wzbogacanie oparte na AI podczas indeksowania. |
| [Ładowanie dokumentów](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Pokazuje, jak przekazywać lub scalać dokumenty w indeks w operacji [importowania danych](search-what-is-data-import.md) . |
| [Składnia zapytań](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Pokazuje, jak skonfigurować [podstawowe zapytanie](search-query-overview.md). |

## <a name="documentation-samples"></a>Przykłady dokumentacji

Następujące przykłady zawierają skojarzony artykuł w [dokumentacji usługi Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/).

| Samples | Opis | 
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | Kod źródłowy do [szybkiego startu: Tworzenie indeksu wyszukiwania w języku Java](search-get-started-java.md). Ten przykład wywołuje interfejsy API REST. |
| [Search-Java-indeksator-Demonstracja](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Pokazuje indeksator Azure Cosmos DB w języku Java. Ten przykład wywołuje interfejsy API REST. |