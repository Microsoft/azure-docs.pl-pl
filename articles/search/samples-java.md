---
title: Przykłady w języku Java
titleSuffix: Azure Cognitive Search
description: Znajdź przykłady kodu w języku Java dla platformy Azure Wyszukiwanie poznawcze, które używają zestawu Azure .NET SDK dla języka Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955041"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Przykłady kodu Java dla platformy Azure Wyszukiwanie poznawcze

Zapoznaj się z przykładami kodu Java, które demonstrują funkcje i przepływ pracy rozwiązania Wyszukiwanie poznawcze platformy Azure. Te przykłady używają [**biblioteki klienta wyszukiwanie poznawcze platformy Azure**](/java/api/overview/azure/search-documents-readme) dla [**zestawu Azure SDK dla języka Java**](/azure/developer/java/sdk), którą można eksplorować za pomocą poniższych linków.

| Cel | Link |
|--------|------|
| Pobieranie pakietu | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| Dokumentacja interfejsu API | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| Przypadki testowe interfejsu API | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Kod źródłowy | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>Przykłady zestawu SDK

Przykłady kodu z zespołu deweloperskiego zestawu Azure SDK przedstawiają użycie interfejsu API. Te przykłady można znaleźć na [**platformie Azure/Azure-SDK — for Java/Tree/Master/SDK/Search/Azure-Search-Documents/src/Samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) w witrynie GitHub.

| Samples | Opis |
|---------|-------------|
| [Tworzenie indeksu wyszukiwania](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Demonstruje sposób tworzenia [indeksów wyszukiwania](search-what-is-an-index.md). |
| [Tworzenie synonimu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Pokazuje, jak tworzyć [mapy synonimów](search-synonyms.md).  |
| [Tworzenie indeksatora wyszukiwania](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Pokazuje, jak tworzyć [indeksatory](search-indexer-overview.md). |
| [Tworzenie źródła danych indeksatora wyszukiwania](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Pokazuje, jak tworzyć źródła danych indeksatora wymagane dla indeksowania opartego na indeksatorze dla [obsługiwanych źródeł danych platformy Azure](search-indexer-overview.md#supported-data-sources). |
| [Tworzenie zestawu umiejętności](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Pokazuje, w jaki sposób tworzyć [umiejętności](cognitive-search-working-with-skillsets.md) dołączone indeksatory, które wykonują wzbogacanie oparte na AI podczas indeksowania. |
| [Ładowanie dokumentów](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Pokazuje, jak przekazywać lub scalać dokumenty w indeks w operacji [importowania danych](search-what-is-data-import.md) . |
| [Składnia zapytań](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Pokazuje, jak skonfigurować [podstawowe zapytanie](search-query-overview.md). |

## <a name="doc-samples"></a>Przykłady doc

Przykłady kodu z zespołu Wyszukiwanie poznawcze przedstawiają funkcje i przepływy pracy. Wiele z tych przykładów odwołuje się do samouczków, przewodników Szybki Start i artykułów z krokami. Te przykłady można znaleźć na [**platformie Azure — przykłady/Azure-Search-Java-Samples**](https://github.com/Azure-Samples/azure-search-java-samples) w witrynie GitHub.

| Samples | Artykuł | 
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Kod źródłowy do [szybkiego startu: Tworzenie indeksu wyszukiwania w języku Java i REST](search-get-started-java.md). Ten przykład nie został zaktualizowany dla zestawu Java SDK. Wywołuje interfejsy API REST. |

> [!Tip]
> Wypróbuj [przeglądarkę przykładów](/samples/browse/?languages=java&products=azure-cognitive-search) , aby wyszukać przykłady kodu firmy Microsoft w witrynie GitHub, filtrować według produktu, usługi i języka.

## <a name="other-samples"></a>Inne przykłady

Poniższe przykłady są również publikowane przez zespół Wyszukiwanie poznawcze, ale nie są przywoływane w dokumentacji. Skojarzone pliki Readme zawierają instrukcje dotyczące użycia.

| Samples | Opis |
|---------|-------------|
| [Wyszukiwanie — Java — wprowadzenie](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Używa biblioteki klienta zestawu SDK języka Java do tworzenia, ładowania i wykonywania zapytań względem indeksu wyszukiwania. Ten przykład jest obecnie autonomiczny. |
| [Search-Java-indeksator-Demonstracja](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Pokazuje indeksator Azure Cosmos DB w języku Java. Ten przykład nie został zaktualizowany dla zestawu Java SDK. Wywołuje interfejsy API REST.|