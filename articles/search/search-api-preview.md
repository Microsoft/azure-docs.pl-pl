---
title: Lista funkcji w wersji zapoznawczej
titleSuffix: Azure Cognitive Search
description: Funkcje w wersji zapoznawczej są wystawiane, dzięki czemu klienci mogą doświadczać opinię na temat projektu i narzędzia. Ten artykuł zawiera kompleksową listę wszystkich funkcji dostępnych w wersji zapoznawczej.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: fb99530923f25541316fd159a2270f7035a1916b
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229248"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funkcje w wersji zapoznawczej na platformie Azure Wyszukiwanie poznawcze

Ten artykuł zawiera kompleksową listę wszystkich funkcji, które są w wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkcje wersji zapoznawczej, które przechodzą do ogólnej dostępności, są usuwane z tej listy. Jeśli funkcja nie jest wymieniona poniżej, można założyć, że jest ona ogólnie dostępna. Aby poznać anonse dotyczące ogólnej dostępności, zobacz [Aktualizacje usług](https://azure.microsoft.com/updates/?product=search) lub [nowości](whats-new.md).

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategoria | Opis | Dostępność  |
|---------|------------------|-------------|---------------|
| [**featuremode — parametr**](https://docs.microsoft.com/rest/api/searchservice/search-documents#featuresmode) | Istotność (ocenianie) | Rozszerzanie wyniku dopasowania do uwzględnienia szczegółów: wynikowe wyniki dla pola, częstotliwość poszczególnych pól oraz Liczba pasujących tokenów dla każdego pola. Te punkty danych można wykorzystać w [niestandardowych rozwiązaniach do oceniania](https://github.com/Azure-Samples/search-ranking-tutorial). | Dodaj ten parametr zapytania przy użyciu opcji [Wyszukaj dokumenty (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) z interfejsem API-Version = 2020-06 -30 — wersja zapoznawcza lub 2019-05-06 — wersja zapoznawcza. |
| [**Tożsamość usługi zarządzanej**](search-howto-managed-identities-data-sources.md) | Indeksatory, zabezpieczenia| Zarejestruj usługę wyszukiwania przy użyciu Azure Active Directory, aby udostępnić ją jako zaufaną usługę, a następnie użyj uprawnień RBAC w źródłach danych platformy Azure, aby umożliwić dostęp tylko do odczytu za pomocą indeksatora. | Uzyskaj dostęp do tej funkcji w przypadku korzystania z portalu lub [tworzenia źródła danych (REST)](https://docs.microsoft.com/rest/api/searchservice/create-data-source) z interfejsem API-Version = 2020-06 -30 — wersja zapoznawcza lub API-Version = 2019-05 -06-Preview. |
| [**Sesje debugowania**](cognitive-search-debug-session.md) | Portal, wzbogacanie AI (zestawu umiejętności) | Edytor zestawu umiejętności w sesji używany do badania i rozwiązywania problemów z zestawu umiejętności. Poprawki stosowane podczas sesji debugowania można zapisać w zestawu umiejętności w usłudze. | Tylko Portal przy użyciu linków strony średniej na stronie Przegląd, aby otworzyć sesję debugowania. |
| [**Usuwanie nietrwałe natywnych obiektów BLOB**](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | Indeksatory, obiekty blob platformy Azure| Usługa Azure Blob Storage indeksator w usłudze Azure Wyszukiwanie poznawcze będzie rozpoznawać obiekty blob w stanie nietrwałego usunięcia i usunąć odpowiednie dokumenty wyszukiwania podczas indeksowania. | Dodaj to ustawienie konfiguracji przy użyciu polecenia [Create indeksatorer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) z interfejsem API-Version = 2020-06 -30 — wersja zapoznawcza lub API-Version = 2019-05 -06 — wersja zapoznawcza. |
| [**Umiejętność wyszukiwania jednostek niestandardowych**](cognitive-search-skill-custom-entity-lookup.md ) | Wzbogacanie AI (zestawu umiejętności) | Umiejętność, która szuka tekstu na podstawie niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz. Korzystając z tej listy, etykieta wszystkie dokumenty z dowolnymi zgodnymi jednostkami. Umiejętność obsługuje również stopień dopasowywania rozmytego, który można zastosować, aby znaleźć dopasowania, które są podobne, ale nie są bardzo dokładne. | Zastąp tę umiejętność w wersji zapoznawczej przy użyciu edytora zestawu umiejętności w portalu lub [Utwórz zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) z interfejsem API-Version = 2020-06 -30 — wersja zapoznawcza lub interfejs API-Version = 2019-05 -06 — wersja zapoznawcza. |
| [**Umiejętność wykrywania przez dane OSOBowe**](cognitive-search-skill-pii-detection.md) | Wzbogacanie AI (zestawu umiejętności) | Umiejętność, która jest używana podczas indeksowania, która wyodrębnia dane osobowe z tekstu wejściowego i umożliwia maskowanie tego tekstu na różne sposoby. | Zastąp tę umiejętność w wersji zapoznawczej przy użyciu edytora zestawu umiejętności w portalu lub [Utwórz zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) z interfejsem API-Version = 2020-06 -30 — wersja zapoznawcza lub interfejs API-Version = 2019-05 -06 — wersja zapoznawcza. |
| [**Wzbogacanie przyrostowe**](cognitive-search-incremental-indexing-conceptual.md) | Konfiguracja indeksatora| Dodaje pamięć podręczną do potoku wzbogacania, umożliwiając ponowne użycie istniejących danych wyjściowych, jeśli docelowa modyfikacja, taka jak aktualizacja zestawu umiejętności lub innego obiektu, nie zmienia zawartości. Buforowanie dotyczy tylko ulepszonych dokumentów utworzonych przez zestawu umiejętności.| Dodaj to ustawienie konfiguracji przy użyciu polecenia [Create indeksatorer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) z interfejsem API-Version = 2020-06 -30 — wersja zapoznawcza lub API-Version = 2019-05 -06 — wersja zapoznawcza. |
| [**Cosmos DB Indexer: MongoDB API, Gremlin API, interfejs API Cassandra**](search-howto-index-cosmosdb.md) | Źródło danych indeksatora | W przypadku Cosmos DB interfejs API SQL jest ogólnie dostępny, ale interfejsy API MongoDB, Gremlin i Cassandra są w wersji zapoznawczej. | Tylko dla Gremlin i Cassandra należy [najpierw utworzyć konto](https://aka.ms/azure-cognitive-search/indexer-preview) , aby można było włączyć obsługę subskrypcji w zapleczu. Źródła danych MongoDB można skonfigurować w portalu. W przeciwnym razie konfiguracja źródła danych dla wszystkich trzech interfejsów API jest obsługiwana przy użyciu polecenia [Create Data Source (REST)](https://docs.microsoft.com/rest/api/searchservice/create-datasource) z interfejsem API-Version = 2020-06 -30 — wersja zapoznawcza lub API-Version = 2019-05 -06-Preview. |
|  [**Azure Data Lake Storage Gen2 indeksator**](search-howto-index-azure-data-lake-storage.md) | Źródło danych indeksatora | Indeksuj zawartość i metadane z Data Lake Storage Gen2.| [Rejestracja](https://aka.ms/azure-cognitive-search/indexer-preview) jest wymagana, aby można było włączyć obsługę subskrypcji w zapleczu. Uzyskaj dostęp do tego źródła danych przy użyciu polecenia [Create Data Source (REST)](https://docs.microsoft.com/rest/api/searchservice/create-datasource) z interfejsem API-Version = 2020-06 -30 — wersja zapoznawcza lub API-Version = 2019-05 -06 — wersja zapoznawcza. |
| [**moreLikeThis**](search-more-like-this.md) | Zapytanie | Znajduje dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została zamieszczona w wcześniejszych wersjach zapoznawczych. | Dodaj ten parametr zapytania w wywołaniach [dokumentów wyszukiwania (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) z interfejsem API-Version = 2020-06 -30 — wersja zapoznawcza, 2019-05-06 — wersja zapoznawcza, 2016-09-01 — wersja zapoznawcza lub 2017-11-11 — wersja zapoznawcza. |

## <a name="calling-preview-rest-apis"></a>Wywoływanie interfejsów API REST w wersji zapoznawczej

Usługa Azure Wyszukiwanie poznawcze zawsze wstępnie zwalnia funkcje eksperymentalne za pomocą interfejsu API REST, a następnie za pomocą wersji wstępnej zestawu .NET SDK.

Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania, a celem zebrania opinii na temat projektowania i implementacji funkcji. Z tego powodu funkcje wersji zapoznawczej mogą ulec zmianie z upływem czasu, prawdopodobnie w sposób powodujący przerwanie zgodności z poprzednimi wersjami. Jest to w przeciwieństwie do funkcji w wersji GA, które są stabilne i mało prawdopodobne, aby można je było zmienić z wyjątkiem małych poprawek i ulepszeń zgodnych z poprzednimi wersjami. Ponadto funkcje wersji zapoznawczej nie zawsze sprawiają, że są one w wersji GA.

W przypadku niektórych funkcji w wersji zapoznawczej w portalu i zestawie .NET SDK interfejs API REST zawsze ma funkcje w wersji zapoznawczej.

+ W przypadku operacji wyszukiwania [**`2020-06-30-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-preview) jest to bieżąca wersja zapoznawcza.

+ W przypadku operacji zarządzania [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) jest to bieżąca wersja zapoznawcza.

Starsze wersje zapoznawcze nadal działają, ale stają się nieodświeżone w czasie. Jeśli kod wywołuje `api-version=2019-05-06-Preview` lub `api-version=2016-09-01-Preview` lub `api-version=2017-11-11-Preview` , te wywołania są nadal ważne. Jednak tylko Najnowsza wersja zapoznawcza jest odświeżana z ulepszeniami. 

Poniższa przykładowa składnia ilustruje wywołanie wersji interfejsu API podglądu.

```HTTP
GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2020-06-30-Preview
```

Usługa Azure Wyszukiwanie poznawcze jest dostępna w wielu wersjach. Aby uzyskać więcej informacji, zobacz [wersje interfejsu API](search-api-versions.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API REST usługi Search w wersji zapoznawczej. Jeśli wystąpią problemy, poproś nas o pomoc dotyczącą [Stack Overflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Search (wersja zapoznawcza)](https://docs.microsoft.com/rest/api/searchservice/index-preview)