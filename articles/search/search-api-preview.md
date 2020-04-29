---
title: Funkcje w wersji zapoznawczej interfejsu API REST
titleSuffix: Azure Cognitive Search
description: Interfejs API REST usługi Wyszukiwanie poznawcze platformy Azure w wersji 2019-05-06 — wersja zapoznawcza obejmuje funkcje eksperymentalne, takie jak magazyn wiedzy i buforowanie indeksatora w celu wzbogacania przyrostowego.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162280"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funkcje w wersji zapoznawczej na platformie Azure Wyszukiwanie poznawcze

W tym artykule wymieniono funkcje obecnie dostępne w wersji zapoznawczej. Funkcje, które przechodzą z wersji zapoznawczej do ogólnej dostępności, są usuwane z tej listy. Możesz sprawdzić [Aktualizacje usług](https://azure.microsoft.com/updates/?product=search) lub [co nowego](whats-new.md) w anonsach dotyczących ogólnej dostępności.

W przypadku niektórych funkcji w wersji zapoznawczej w portalu i zestawie .NET SDK interfejs API REST zawsze ma funkcje w wersji zapoznawczej.

+ W przypadku operacji wyszukiwania [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) jest to bieżąca wersja zapoznawcza.
+ W przypadku operacji zarządzania [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) jest to bieżąca wersja zapoznawcza.

> [!IMPORTANT]
> Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Funkcje wzbogacania AI

Zapoznaj się z najnowszymi ulepszeniami wzbogacenia AI za pomocą [interfejsu API wyszukiwania w wersji zapoznawczej](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

|||
|-|-|
| [Umiejętność wyszukiwania jednostek niestandardowych (wersja zapoznawcza)](cognitive-search-skill-custom-entity-lookup.md ) | Umiejętność, która szuka tekstu na podstawie niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz. Korzystając z tej listy, etykieta wszystkie dokumenty z dowolnymi zgodnymi jednostkami. Umiejętność obsługuje również stopień dopasowywania rozmytego, który można zastosować, aby znaleźć dopasowania, które są podobne, ale nie są bardzo dokładne. | 
| [Umiejętność wykrywania przez dane OSOBowe (wersja zapoznawcza)](cognitive-search-skill-pii-detection.md) | Umiejętność, która jest używana podczas indeksowania, która wyodrębnia dane osobowe z tekstu wejściowego i umożliwia maskowanie tego tekstu na różne sposoby.| 
| [Wzbogacanie przyrostowe (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) | Dodaje pamięć podręczną do potoku wzbogacania, umożliwiając ponowne użycie istniejących danych wyjściowych, jeśli docelowa modyfikacja, taka jak aktualizacja zestawu umiejętności lub innego obiektu, nie zmienia zawartości. Buforowanie dotyczy tylko ulepszonych dokumentów utworzonych przez zestawu umiejętności.| 
| [Magazyn wiedzy (wersja zapoznawcza)](knowledge-store-concept-intro.md) | Nowe miejsce docelowe potoku wzbogacania opartego na AI. Struktura danych fizycznych istnieje w usłudze Azure Blob Storage i Azure Table Storage oraz jest tworzona i wypełniana podczas uruchamiania indeksatora, który ma dołączony zestawu umiejętności poznawcze. Definicja samego sklepu wiedzy została określona w definicji zestawu umiejętności. W ramach definicji sklepu merytorycznego można kontrolować struktury fizyczne danych za pomocą elementów *projekcji* , które określają, jak dane są w kształcie, czy dane są przechowywane w magazynie tabel lub w magazynie obiektów blob, oraz czy istnieje wiele widoków.| 

## <a name="indexing-and-query-features"></a>Funkcje indeksowania i zapytania

Funkcje programu Indexer Preview są dostępne w interfejsie API wyszukiwania w wersji zapoznawczej. 

|||
|-|-|
| [Indeksator usługi Cosmos DB](search-howto-index-cosmosdb.md) | Obsługa typów interfejsów API MongoDB API (wersja zapoznawcza), Gremlin API (wersja zapoznawcza) i interfejs API Cassandra (wersja zapoznawcza). | 
|  [Indeksator Azure Data Lake Storage Gen2 (wersja zapoznawcza)](search-howto-index-azure-data-lake-storage.md) | Indeksuj zawartość i metadane z Data Lake Storage Gen2.| 
| [moreLikeThis — parametr zapytania (wersja zapoznawcza)](search-more-like-this.md) | Znajduje dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została zamieszczona w wcześniejszych wersjach zapoznawczych. | 

## <a name="management-features"></a>Funkcje zarządzania

|||
|-|-|
| [Obsługa prywatnego punktu końcowego](service-create-private-endpoint.md) | Można utworzyć sieć wirtualną z bezpiecznym klientem (na przykład maszyną wirtualną), a następnie utworzyć usługę wyszukiwania używającą prywatnego punktu końcowego. |
| Ograniczenie dostępu do adresów IP | Za [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) pomocą interfejsu API REST zarządzania można utworzyć usługę, która ma ograniczenia dotyczące dozwolonych adresów IP. |

## <a name="earlier-preview-features"></a>Wcześniejsze funkcje w wersji zapoznawczej

Funkcje ogłoszone we wcześniejszych wersjach zapoznawczych, jeśli nie przechodzą do ogólnej dostępności, nadal są w publicznej wersji zapoznawczej. Jeśli wywołujesz interfejs API z wcześniejszą wersją interfejsu API w wersji zapoznawczej, możesz nadal korzystać z tej wersji lub `2019-05-06-Preview` przełączać się do programu bez zmian w oczekiwanym zachowaniu.

## <a name="how-to-call-a-preview-api"></a>Jak wywołać interfejs API w wersji zapoznawczej

Starsze wersje zapoznawcze nadal działają, ale stają się nieodświeżone w czasie. Jeśli kod wywołuje `api-version=2016-09-01-Preview` lub `api-version=2017-11-11-Preview`, te wywołania są nadal ważne. Jednak tylko Najnowsza wersja zapoznawcza jest odświeżana z ulepszeniami. 

Poniższa przykładowa składnia ilustruje wywołanie wersji interfejsu API podglądu.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Usługa Azure Wyszukiwanie poznawcze jest dostępna w wielu wersjach. Aby uzyskać więcej informacji, zobacz [wersje interfejsu API](search-api-versions.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API REST usługi Search. Jeśli wystąpią problemy, poproś nas o pomoc dotyczącą usługi [StackOverflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Search](https://docs.microsoft.com/rest/api/searchservice/)