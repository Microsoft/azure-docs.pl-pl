---
title: Przykłady w języku Python
titleSuffix: Azure Cognitive Search
description: Znajdź przykłady kodu w języku Python platformy Azure Wyszukiwanie poznawcze, które używają zestawu Azure .NET SDK dla języka Python lub REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955126"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Przykłady kodu w języku Python dla usługi Azure Wyszukiwanie poznawcze

Zapoznaj się z przykładami kodu w języku Python, które demonstrują funkcje i przepływ pracy rozwiązania Wyszukiwanie poznawcze platformy Azure. Te przykłady używają [**biblioteki klienta wyszukiwanie poznawcze platformy Azure**](/python/api/overview/azure/search-documents-readme) dla [**zestawu Azure SDK dla języka Python**](/azure/developer/python/), którą można eksplorować za pomocą poniższych linków.

| Cel | Link |
|--------|------|
| Pobieranie pakietu | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| Dokumentacja interfejsu API | [Azure-Search-Documents](/python/api/azure-search-documents)  |
| Przypadki testowe interfejsu API | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Kod źródłowy | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>Przykłady zestawu SDK

Przykłady kodu z zespołu deweloperskiego zestawu Azure SDK przedstawiają użycie interfejsu API. Te przykłady można znaleźć w temacie [**Azure-SDK — for Python/Tree/Master/SDK/Search/Azure-Search-Documents/Samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) w witrynie GitHub.

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

## <a name="doc-samples"></a>Przykłady doc

Przykłady kodu z zespołu Wyszukiwanie poznawcze przedstawiają funkcje i przepływy pracy. Wiele z tych przykładów odwołuje się do samouczków, przewodników Szybki Start i artykułów z krokami. Te przykłady można znaleźć na [**platformie Azure — przykłady/Azure-Search-Python-Samples**](https://github.com/Azure-Samples/azure-search-python-samples) w witrynie GitHub.

| Samples | Artykuł |
|---------|---------|
| [Start](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Kod źródłowy do [szybkiego startu: Tworzenie indeksu wyszukiwania w języku Python](search-get-started-python.md). W tym artykule opisano podstawowy przepływ pracy służący do tworzenia, ładowania i wykonywania zapytań dotyczących indeksu wyszukiwania przy użyciu przykładowych danych. |
| [Samouczek — wzbogacanie AI](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Kod źródłowy [samouczka: Użyj języka Python i AI do generowania zawartości z możliwością wyszukiwania z obiektów blob platformy Azure](cognitive-search-tutorial-blob-python.md). W tym artykule przedstawiono sposób tworzenia indeksatora obiektów blob z zestawu umiejętności poznawczym, w którym zestawu umiejętności tworzy i przekształca pierwotną zawartość w celu przeszukiwania lub użycia. |
| [Azure — niestandardowa — umiejętność](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Kod źródłowy na [przykład: Utwórz niestandardową umiejętność przy użyciu języka Python](cognitive-search-custom-skill-python.md). W tym artykule przedstawiono integrację indeksatora i zestawu umiejętności z modelami uczenia głębokiego w Azure Machine Learning. |

> [!Tip]
> Wypróbuj [przeglądarkę przykładów](/samples/browse/?languages=python&products=azure-cognitive-search) , aby wyszukać przykłady kodu firmy Microsoft w witrynie GitHub, filtrować według produktu, usługi i języka.