---
title: Przykłady dla platformy .NET
titleSuffix: Azure Cognitive Search
description: Znajdź przykłady kodu w języku C# dla platformy Azure Wyszukiwanie poznawcze, które używają bibliotek klienckich platformy .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: cbb84a4934eed4d258cf07772753315785f98019
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99218166"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Przykłady kodu dla platformy .NET (C#) dla usługi Azure Wyszukiwanie poznawcze

Zapoznaj się z przykładami kodu w języku C#, które demonstrują funkcje i przepływ pracy rozwiązania Wyszukiwanie poznawcze platformy Azure. Te przykłady używają [**biblioteki klienta wyszukiwanie poznawcze platformy Azure**](/dotnet/api/overview/azure/search) dla [**zestawu Azure SDK dla platformy .NET**](/dotnet/azure/), którą można eksplorować za pomocą poniższych linków.

| Cel | Link |
|--------|------|
| Pobieranie pakietu | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| Dokumentacja interfejsu API | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| Przypadki testowe interfejsu API | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/testy](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Kod źródłowy | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>Przykłady zestawu SDK

Przykłady kodu z zespołu deweloperskiego zestawu Azure SDK przedstawiają użycie interfejsu API. Te przykłady można znaleźć na [**platformie Azure/Azure-SDK-for-NET/Tree/Master/SDK/Search/Azure.Search.Documents/Samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) w witrynie GitHub.

| Samples | Opis |
|---------|-------------|
| ["Hello World", synchronicznie](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Pokazuje, jak utworzyć klienta, uwierzytelniać i obsługiwać błędy przy użyciu metod synchronicznych.|
| ["Hello World", asynchronicznie](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Pokazuje, jak utworzyć klienta, uwierzytelniać i obsługiwać błędy przy użyciu metod asynchronicznych.  |
| [Operacje na poziomie usług](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Pokazuje, jak tworzyć indeksy, indeksatory, źródła danych, umiejętności i mapy synonimów. Ten przykład pokazuje również, jak uzyskać statystyki usługi i jak zbadać indeks.  |
| [Operacje indeksowania](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Pokazuje, jak wykonać akcję na istniejącym indeksie, w tym przypadku pobierając liczbę dokumentów przechowywanych w indeksie.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Demonstruje technikę pracy z nieobsługiwanymi typami danych.  |
| [Indeksowanie dokumentów (wypychanie modelu)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | "Push" — indeksowanie modelu, w którym wysyłasz ładunek JSON do indeksu w usłudze.   |
| [Przykład klucza szyfrowania](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Demonstruje użycie klucza szyfrowania zarządzanego przez klienta w celu dodania dodatkowej warstwy ochrony do zawartości poufnej.  |

## <a name="doc-samples"></a>Przykłady doc

Przykłady kodu z zespołu Wyszukiwanie poznawcze przedstawiają funkcje i przepływy pracy. Wiele z tych przykładów odwołuje się do samouczków, przewodników Szybki Start i artykułów z krokami. Te przykłady można znaleźć na [**platformie Azure — przykłady/Azure-Search-dotnet-Samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) oraz na [**platformie Azure — przykłady/wyszukiwanie-dotnet — wprowadzenie**](https://github.com/Azure-Samples/search-dotnet-getting-started/) do serwisu GitHub.

| Samples | Artykuł  |
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Kod źródłowy do [szybkiego startu: Tworzenie indeksu wyszukiwania ](search-get-started-dotnet.md). W tym artykule opisano podstawowy przepływ pracy służący do tworzenia, ładowania i wykonywania zapytań dotyczących indeksu wyszukiwania przy użyciu przykładowych danych. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Kod źródłowy służący do [korzystania z biblioteki klienckiej platformy .NET](search-howto-dotnet-sdk.md). W tym artykule opisano podstawowe przepływy pracy, ale bardziej szczegółowe omówienie użycia interfejsu API.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Kod źródłowy na [przykład: Dodawanie synonimów w języku C#](search-synonyms-tutorial-sdk.md). Listy synonimów są używane na potrzeby rozszerzania zapytania i dające dopasowane warunki, które są zewnętrzne względem indeksu. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Kod źródłowy dla [samouczka: indeksowanie danych usługi Azure SQL przy użyciu zestawu .NET SDK](search-indexer-tutorial.md). W tym artykule opisano sposób konfigurowania indeksatora usługi Azure SQL, który ma harmonogram, mapowania pól i parametry.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Kod źródłowy służący do [konfigurowania kluczy zarządzanych przez klienta na potrzeby szyfrowania danych](search-security-manage-encryption-keys.md). |
| [Tworzenie pierwszej aplikacji w języku C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Kod źródłowy [samouczka: Tworzenie pierwszej aplikacji wyszukiwania](tutorial-csharp-create-first-app.md). Chociaż większość przykładów jest aplikacjami konsolowymi, ten przykład MVC korzysta ze strony sieci Web w celu uzyskania przykładowego indeksu hoteli, pokazując podstawowe wyszukiwanie, stronicowanie, Autouzupełnianie i sugerowane zapytania, aspekty i filtry. |
| [wiele źródeł danych](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Kod źródłowy [samouczka: indeks z wielu źródeł danych](tutorial-multiple-data-sources.md). |
|  [Optymalizacja — indeksowanie danych](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Kod źródłowy [samouczka: Optymalizacja indeksowania przy użyciu interfejsu API wypychania](tutorial-optimize-indexing-push-api.md).  |
| [Samouczek — wzbogacanie AI](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Kod źródłowy [samouczka: wygenerowanej przez program AI zawartość przeszukiwaną z obiektów blob platformy Azure przy użyciu zestawu .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Wypróbuj [przeglądarkę przykładów](/samples/browse/?languages=csharp&products=azure-cognitive-search) , aby wyszukać przykłady kodu firmy Microsoft w witrynie GitHub, filtrować według produktu, usługi i języka.

## <a name="other-samples"></a>Inne przykłady

Poniższe przykłady są również publikowane przez zespół Wyszukiwanie poznawcze, ale nie są przywoływane w dokumentacji. Skojarzone pliki Readme zawierają instrukcje dotyczące użycia.

| Samples | Opis |
|---------|-------------|
| [Azure — wyszukiwanie-energia](https://github.com/Azure-Samples/azure-search-power-skills)  | Kod źródłowy do konsumowanych umiejętności niestandardowych, które można uwzględnić w kupionych rozwiązaniach.  |
| [Akcelerator rozwiązań do zdobywania wiedzy](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Obejmuje szablony, pliki obsługi i raporty analityczne, które ułatwiają prototyp kompleksowego rozwiązania do wyszukiwania w bazie wiedzy.  |
| [Repozytorium aplikacji Covid-19](https://github.com/liamca/covid19search) | Repozytorium kodu źródłowego dla [aplikacji wyszukiwania Covid-19](https://covid19search.azurewebsites.net/) opartej na wyszukiwanie poznawcze |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Dowiedz się więcej o [rozwiązaniu JFK](https://www.microsoft.com/ai/ai-lab-jfk-files). |
| [Wyszukaj i akcelerator QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | [Rozwiązanie](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381) łączące możliwości wyszukiwania i QNA Maker. Zobacz [witrynę demonstracji](https://aka.ms/qnaWithAzureSearchDemo)na żywo. |