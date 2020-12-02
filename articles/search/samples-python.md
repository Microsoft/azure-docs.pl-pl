---
title: Przykłady w języku Python
titleSuffix: Azure Cognitive Search
description: Znajdź przykłady kodu w języku Python platformy Azure Wyszukiwanie poznawcze, które używają zestawu Azure .NET SDK dla języka Python lub REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 244408cc512a51683ecb3c062113170b1c7f4781
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499564"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Przykłady kodu w języku Python dla usługi Azure Wyszukiwanie poznawcze

Zapoznaj się z przykładami kodu w języku Python, które demonstrują funkcje i funkcje Wyszukiwanie poznawcze platformy Azure. Główne repozytoria są następujące:

| Repozytorium | Opis |
|------------|-------------|
| [Azure-SDK — for Python/Tree/Master/SDK/Search/Azure-Search-Documents/Samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Przykłady wytwarzane przez zespół zestawu Azure SDK, który jest dostarczany z Azure.Search.Docą uments klienta w zestawie SDK. Możesz również przejrzeć [testy jednostkowe](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) dla biblioteki klienta, aby zobaczyć, jak są wywoływane różne interfejsy API. |
| [Azure-Samples/Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples) | Przykłady kodu, które są dołączone do artykułów, [w tym szybkiego startu: Tworzenie indeksu wyszukiwania w języku Python](search-get-started-python.md).|

> [!Tip]
> Wypróbuj [przeglądarkę przykładów](/samples/browse/?languages=csharp&products=azure-cognitive-search) , aby wyszukać przykłady kodu firmy Microsoft w witrynie GitHub, filtrować według produktu, usługi i języka.

## <a name="python-sdk-samples"></a>Przykłady zestawów SDK dla języka Python

Zestaw Azure SDK dla języka Python zawiera wiele przykładów i [stronę wprowadzającą](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) , która zawiera wymagania wstępne i instalację pakietu. Strona zawiera również linki do poniższych przykładów, które są wymienione w tym miejscu dla wygody użytkownika.

| Samples | Opis |
|---------|-------------|
| [Uwierzytelnianie](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Pokazuje, jak skonfigurować klienta i uwierzytelniać się w usłudze. | 
| [Operacje Create-Read-Update-Delete](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [indeksy wyszukiwania](search-what-is-an-index.md). |
| [Operacje Create-Read-Update-Delete dla indeksatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Pokazuje, jak tworzyć, aktualizować, pobierać i usuwać [indeksatory](search-indexer-overview.md)oraz z nich korzystać. |
| [Wyszukaj źródła danych indeksatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Pokazuje, jak tworzyć, aktualizować, pobierać i usuwać źródła danych indeksatora wymagane dla indeksowania opartego na indeksatorach [obsługiwanych źródeł danych platformy Azure](search-indexer-overview.md#supported-data-sources). |
| [Synonimy](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [mapy synonimów](search-synonyms.md).  |
| [Ładowanie dokumentów](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Pokazuje, jak przekazywać lub scalać dokumenty w indeks w operacji [importowania danych](search-what-is-data-import.md) . |
| [Proste zapytanie](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Pokazuje, jak skonfigurować [podstawowe zapytanie](search-query-overview.md). |
| [Zapytanie filtru](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Pokazuje, jak skonfigurować [wyrażenie filtru](search-filters.md). |
| [Zapytanie aspektu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Ilustruje pracę z [aspektami](search-filters-facets.md). |

## <a name="documentation-samples"></a>Przykłady dokumentacji

Następujące przykłady zawierają skojarzony artykuł w [dokumentacji usługi Azure wyszukiwanie poznawcze](./index.yml).

| Samples | Opis | 
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Kod źródłowy do [szybkiego startu: Tworzenie indeksu wyszukiwania w języku Python](search-get-started-python.md).  |
| [Samouczek — wzbogacanie AI](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Kod źródłowy [samouczka: Użyj języka Python i AI do generowania zawartości z możliwością wyszukiwania z obiektów blob platformy Azure](cognitive-search-tutorial-blob-python.md).  |
| [Azure — niestandardowa — umiejętność](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Kod źródłowy na [przykład: Utwórz niestandardową umiejętność przy użyciu języka Python](cognitive-search-custom-skill-python.md).  |