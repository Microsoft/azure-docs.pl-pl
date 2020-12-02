---
title: Przykłady dla platformy .NET
titleSuffix: Azure Cognitive Search
description: Znajdź przykłady kodu w języku C# dla platformy Azure Wyszukiwanie poznawcze, które używają bibliotek klienckich platformy .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: ab6408621616a4be62631391456f73e90fced752
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499003"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Przykłady kodu dla platformy .NET (C#) dla usługi Azure Wyszukiwanie poznawcze

Zapoznaj się z przykładami kodu w języku C#, które demonstrują funkcje i funkcje Wyszukiwanie poznawcze platformy Azure. Główne repozytoria są następujące:

| Repozytorium | Opis |
|------------|-------------|
| [Azure-SDK-for-NET/SDK/Search/Azure.Search.Documents/Samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Przykłady wytwarzane przez zespół zestawu Azure SDK, który jest dostarczany z Azure.Search.Docą uments klienta w zestawie SDK. Możesz również przejrzeć [testy jednostkowe](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) dla biblioteki klienta, aby zobaczyć, jak są wywoływane różne interfejsy API. |
| [Azure-Samples/Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | Przykłady dołączone do artykułów w dokumentacji, w tym [informacje dotyczące korzystania z biblioteki klienta platformy .NET](search-howto-dotnet-sdk.md).|
| [Azure — przykłady/wyszukiwanie — dotnet — rozpoczęcie pracy](https://github.com/Azure-Samples/search-dotnet-getting-started) | Przykłady dołączone do przewodników Szybki Start i samouczków w dokumentacji.|

> [!Tip]
> Wypróbuj [przeglądarkę przykładów](/samples/browse/?languages=csharp&products=azure-cognitive-search) , aby wyszukać przykłady kodu firmy Microsoft w witrynie GitHub, filtrować według produktu, usługi i języka.

## <a name="net-sdk-samples"></a>Przykłady zestawów SDK dla platformy .NET

Zestaw Azure SDK dla platformy .NET zawiera wiele przykładów i [plik Readme z przykładami](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) opisującymi każdą z nich. Ta lista jest pokazana dla wygody użytkownika.

| Samples | Opis |
|---------|-------------|
| ["Hello World", synchronicznie](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Pokazuje, jak utworzyć klienta, uwierzytelniać i obsługiwać błędy przy użyciu metod synchronicznych.|
| ["Hello World", asynchronicznie](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Pokazuje, jak utworzyć klienta, uwierzytelniać i obsługiwać błędy przy użyciu metod asynchronicznych.  |
| [Operacje na poziomie usług](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Pokazuje, jak tworzyć indeksy, indeksatory, źródła danych, umiejętności i mapy synonimów. Ten przykład pokazuje również, jak uzyskać statystyki usługi i jak zbadać indeks.  |
| [Operacje indeksowania](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Pokazuje, jak wykonać akcję na istniejącym indeksie, w tym przypadku pobierając liczbę dokumentów przechowywanych w indeksie.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Demonstruje technikę pracy z nieobsługiwanymi typami danych.  |
| [Indeksowanie dokumentów (wypychanie modelu)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | "Push" — indeksowanie modelu, w którym wysyłasz ładunek JSON do indeksu w usłudze.   |
| [Przykład klucza szyfrowania](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Demonstruje użycie klucza szyfrowania zarządzanego przez klienta w celu dodania dodatkowej warstwy ochrony do zawartości poufnej.  |

## <a name="documentation-samples"></a>Przykłady dokumentacji

Następujące przykłady zawierają skojarzony artykuł w [dokumentacji usługi Azure wyszukiwanie poznawcze](./index.yml).

| Samples | Opis |
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Kod źródłowy do [szybkiego startu: Tworzenie indeksu wyszukiwania ](search-get-started-dotnet.md).  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Kod źródłowy służący do [korzystania z biblioteki klienta .NET](search-howto-dotnet-sdk.md) |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Listy synonimów są używane na potrzeby rozszerzania zapytania i dające dopasowane warunki, które są zewnętrzne względem indeksu. Ten przykład jest uwzględniony w [przykładzie: Dodawanie synonimów w języku C#](search-synonyms-tutorial-sdk.md). |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Kod źródłowy za fragmenty kodu powiązane z indeksatorem w różnych artykułach. Ten przykład pokazuje, jak skonfigurować indeksator, który ma harmonogram, mapowania pól i parametry.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Kod źródłowy służący do [konfigurowania kluczy zarządzanych przez klienta na potrzeby szyfrowania danych](search-security-manage-encryption-keys.md) |
| [Tworzenie pierwszej aplikacji w języku C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Kod źródłowy [samouczka: Tworzenie pierwszej aplikacji wyszukiwania](tutorial-csharp-create-first-app.md). Chociaż większość przykładów jest aplikacjami konsolowymi, ten przykład MVC korzysta ze strony sieci Web w celu uzyskania przykładowego indeksu hoteli, pokazując podstawowe wyszukiwanie, stronicowanie, Autouzupełnianie i sugerowane zapytania, aspekty i filtry. |
| [wiele źródeł danych](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Kod źródłowy [samouczka: indeks z wielu źródeł danych](tutorial-multiple-data-sources.md). |
|  [Optymalizacja — indeksowanie danych](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Kod źródłowy [samouczka: Optymalizacja indeksowania przy użyciu interfejsu API wypychania](tutorial-optimize-indexing-push-api.md).  |
| [Samouczek — wzbogacanie AI](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Kod źródłowy [samouczka: wygenerowanej przez program AI zawartość przeszukiwaną z obiektów blob platformy Azure przy użyciu zestawu .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

## <a name="standalone-samples-and-solutions"></a>Samodzielne przykłady i rozwiązania

| Samples | Opis |
|---------|-------------|
| [Azure — wyszukiwanie-energia](https://github.com/Azure-Samples/azure-search-power-skills)  | Kod źródłowy do konsumowanych umiejętności niestandardowych, które można uwzględnić w kupionych rozwiązaniach.  |
| [Akcelerator rozwiązań do zdobywania wiedzy](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Obejmuje szablony, pliki obsługi i raporty analityczne, które ułatwiają prototyp kompleksowego rozwiązania do wyszukiwania w bazie wiedzy.  |
| [Repozytorium aplikacji Covid-19](https://github.com/liamca/covid19search) | Repozytorium kodu źródłowego dla [aplikacji wyszukiwania Covid-19](https://covid19search.azurewebsites.net/) opartej na wyszukiwanie poznawcze |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Dowiedz się więcej o [rozwiązaniu JFK](https://www.microsoft.com/ai/ai-lab-jfk-files). |