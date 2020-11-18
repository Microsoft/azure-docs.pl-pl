---
title: Przykłady skryptów w języku JavaScript
titleSuffix: Azure Cognitive Search
description: Znajdź przykłady kodu w języku JavaScript platformy Azure Wyszukiwanie poznawcze, które używają zestawu Azure .NET SDK dla języka JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6cd696bf0853b1e6bafc06f2e99b2808970fed25
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686855"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Przykłady kodu JavaScript dla Wyszukiwanie poznawcze platformy Azure

Zapoznaj się z przykładami kodu JavaScript, które demonstrują funkcje i funkcje Wyszukiwanie poznawcze platformy Azure. Główne repozytoria są następujące:

| Repozytorium | Opis |
|------------|-------------|
| [Azure-SDK-for-js/drzewo/wzorzec/zestaw SDK/wyszukiwanie/wyszukiwanie-dokumenty](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Przykłady wytwarzane przez zespół zestawu Azure SDK, który jest dostarczany z Azure.Search.Docą uments klienta w zestawie SDK. Możesz również przejrzeć [testy jednostkowe](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) dla biblioteki klienta, aby zobaczyć, jak są wywoływane różne interfejsy API. |
| [Azure-Samples/Azure-Search-JavaScript-Samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | Przykłady kodu, które są dołączone do artykułów, [w tym Szybki Start: Tworzenie indeksu wyszukiwania w języku JavaScript](search-get-started-javascript.md).|

> [!Tip]
> Wypróbuj [przeglądarkę przykładów](/samples/browse/?languages=csharp&products=azure-cognitive-search) , aby wyszukać przykłady kodu firmy Microsoft w witrynie GitHub, filtrować według produktu, usługi i języka.

## <a name="javascript-sdk-samples"></a>Przykłady zestawu SDK języka JavaScript

Zestaw Azure SDK dla języka Java zawiera wiele przykładów i [stronę wprowadzającą](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) obejmującą instalację pakietów, konfigurację klienta i rozwiązywanie problemów. Na stronie opisano również następujące przykładowe kategorie, które są wymienione w tym miejscu dla wygody użytkownika.

| Samples | Opis |
|---------|-------------|
| [zwiększa](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [indeksy wyszukiwania](search-what-is-an-index.md). Ta przykładowa kategoria zawiera również przykład statystyk usługi. |
| [dataSourceConnections (dla indeksatorów)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Pokazuje, jak tworzyć, aktualizować, pobierać i usuwać źródła danych indeksatora wymagane dla indeksowania opartego na indeksatorach [obsługiwanych źródeł danych platformy Azure](search-indexer-overview.md#supported-data-sources). |
| [indeksatorów](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [indeksatory](search-indexer-overview.md).|
| [Zestawu umiejętności](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [umiejętności](cognitive-search-working-with-skillsets.md) , które są dołączonymi indeksatorami i które wykonują wzbogacanie oparte na AI podczas indeksowania. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [mapy synonimów](search-synonyms.md).  |
| [Zapytania](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Zapytania są tylko do odczytu. To przykładowe zapytanie jest wykonywane względem publicznego indeksu hostowanego przez firmę Microsoft.  |

## <a name="typescript-samples"></a>Przykłady języka TypeScript

Zestaw SDK zawiera również przykłady języka TypeScript, które są wymienione w tym miejscu dla wygody użytkownika.

| Samples | Opis |
|---------|-------------|
| [zwiększa](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [indeksy wyszukiwania](search-what-is-an-index.md). Ta przykładowa kategoria zawiera również przykład statystyk usługi. |
| [dataSourceConnections (dla indeksatorów)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Pokazuje, jak tworzyć, aktualizować, pobierać i usuwać źródła danych indeksatora wymagane dla indeksowania opartego na indeksatorach [obsługiwanych źródeł danych platformy Azure](search-indexer-overview.md#supported-data-sources). |
| [indeksatorów](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [indeksatory](search-indexer-overview.md).|
| [Zestawu umiejętności](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [umiejętności](cognitive-search-working-with-skillsets.md) , które są dołączonymi indeksatorami i które wykonują wzbogacanie oparte na AI podczas indeksowania. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Pokazuje, jak tworzyć, aktualizować, pobierać, wyświetlać i usuwać [mapy synonimów](search-synonyms.md).  |
| [Zapytania](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.js) | Ilustruje wykonywanie zapytania względem publicznego indeksu tylko do odczytu hostowanego przez firmę Microsoft.  |

## <a name="documentation-samples"></a>Przykłady dokumentacji

Następujące przykłady zawierają skojarzony artykuł w [dokumentacji usługi Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/).

| Samples | Opis | 
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/Quickstart) | Kod źródłowy do [szybkiego startu: Tworzenie indeksu wyszukiwania w języku JavaScript](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Przykłady autonomiczne

| Samples | Opis |
|---------|-------------|
| [Azure-Search — reagowanie na szablon](https://github.com/dereklegenzoff/azure-search-react-template) | Szablon reakcji dla Wyszukiwanie poznawcze platformy Azure (github.com) |