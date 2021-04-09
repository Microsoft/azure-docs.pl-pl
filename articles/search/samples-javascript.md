---
title: Przykłady skryptów w języku JavaScript
titleSuffix: Azure Cognitive Search
description: Znajdź przykłady kodu w języku JavaScript platformy Azure Wyszukiwanie poznawcze, które używają zestawu Azure .NET SDK dla języka JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 85a4d6390087100d8d9521f6ac20dbace3a711eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955945"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Przykłady kodu JavaScript dla Wyszukiwanie poznawcze platformy Azure

Zapoznaj się z przykładami kodu JavaScript, które demonstrują funkcje i przepływ pracy rozwiązania Wyszukiwanie poznawcze platformy Azure. Te przykłady używają [**biblioteki klienta wyszukiwanie poznawcze platformy Azure**](/javascript/api/overview/azure/search-documents-readme) dla [**zestawu Azure SDK dla języka JavaScript**](/azure/developer/javascript/), którą można eksplorować za pomocą poniższych linków.

| Cel | Link |
|--------|------|
| Pobieranie pakietu | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| Dokumentacja interfejsu API | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| Przypadki testowe interfejsu API | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Kod źródłowy | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>Przykłady zestawu SDK

Przykłady kodu z zespołu deweloperskiego zestawu Azure SDK przedstawiają użycie interfejsu API. Te przykłady można znaleźć w temacie [**Azure-SDK-for-js/Tree/Master/SDK/Search/Search-Documents/Samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) w witrynie GitHub.

### <a name="javascript-sdk-samples"></a>Przykłady zestawu SDK języka JavaScript

| Samples | Opis |
|---------|-------------|
| [zwiększa](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [indeksy wyszukiwania](search-what-is-an-index.md). Ta przykładowa kategoria zawiera również przykład statystyk usługi. |
| [dataSourceConnections (dla indeksatorów)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Pokazuje, jak tworzyć, aktualizować, pobierać i usuwać źródła danych indeksatora wymagane dla indeksowania opartego na indeksatorach [obsługiwanych źródeł danych platformy Azure](search-indexer-overview.md#supported-data-sources). |
| [indeksatorów](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Pokazuje, jak tworzyć, aktualizować, pobierać i usuwać [indeksatory](search-indexer-overview.md)oraz z nich korzystać.|
| [Zestawu umiejętności](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [umiejętności](cognitive-search-working-with-skillsets.md) , które są dołączonymi indeksatorami i które wykonują wzbogacanie oparte na AI podczas indeksowania. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [mapy synonimów](search-synonyms.md).  |

### <a name="typescript-samples"></a>Przykłady języka TypeScript

| Samples | Opis |
|---------|-------------|
| [zwiększa](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [indeksy wyszukiwania](search-what-is-an-index.md). Ta przykładowa kategoria zawiera również przykład statystyk usługi. |
| [dataSourceConnections (dla indeksatorów)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Pokazuje, jak tworzyć, aktualizować, pobierać i usuwać źródła danych indeksatora wymagane dla indeksowania opartego na indeksatorach [obsługiwanych źródeł danych platformy Azure](search-indexer-overview.md#supported-data-sources). |
| [indeksatorów](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Pokazuje, jak tworzyć, aktualizować, pobierać i usuwać [indeksatory](search-indexer-overview.md)oraz z nich korzystać.|
| [Zestawu umiejętności](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [umiejętności](cognitive-search-working-with-skillsets.md) , które są dołączonymi indeksatorami i które wykonują wzbogacanie oparte na AI podczas indeksowania. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [mapy synonimów](search-synonyms.md).  |

## <a name="doc-samples"></a>Przykłady doc

Przykłady kodu z zespołu Wyszukiwanie poznawcze przedstawiają funkcje i przepływy pracy. Wiele z tych przykładów odwołuje się do samouczków, przewodników Szybki Start i artykułów z krokami. Te przykłady można znaleźć na [**platformie Azure — przykłady/Azure-Search-JavaScript-Samples**](https://github.com/Azure-Samples/azure-search-javascript-samples) w witrynie GitHub.

| Samples | Artykuł |
|---------|---------|
| [Start](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Kod źródłowy do [szybkiego startu: Tworzenie indeksu wyszukiwania w języku JavaScript](search-get-started-javascript.md). W tym artykule opisano podstawowy przepływ pracy służący do tworzenia, ładowania i wykonywania zapytań dotyczących indeksu wyszukiwania przy użyciu przykładowych danych. |

> [!Tip]
> Wypróbuj [przeglądarkę przykładów](/samples/browse/?languages=javascript&products=azure-cognitive-search) , aby wyszukać przykłady kodu firmy Microsoft w witrynie GitHub, filtrować według produktu, usługi i języka.

## <a name="other-samples"></a>Inne przykłady

Poniższe przykłady są również publikowane przez zespół Wyszukiwanie poznawcze, ale nie są przywoływane w dokumentacji. Skojarzone pliki Readme zawierają instrukcje dotyczące użycia.

| Samples | Opis |
|---------|-------------|
| [Azure-Search — reagowanie na szablon](https://github.com/dereklegenzoff/azure-search-react-template) | Szablon reakcji dla Wyszukiwanie poznawcze platformy Azure (github.com) |