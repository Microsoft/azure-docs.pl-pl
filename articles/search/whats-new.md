---
title: Co nowego w usłudze Azure Wyszukiwanie poznawcze
description: Anonse nowych i ulepszonych funkcji, w tym zmiana nazwy usługi Azure Search na platformę Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 606b102b1922d382f479235ffc4c61b68e7db766
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676392"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co nowego w usłudze Azure Wyszukiwanie poznawcze

Dowiedz się, co nowego w usłudze. Oznacz Tę stronę zakładką, aby zapewnić aktualność usługi. Zapoznaj się z [listą funkcji wersji zapoznawczej](search-api-preview.md) , aby wyświetlić kompleksową listę funkcji, które nie są jeszcze ogólnie dostępne.

## <a name="march-2021"></a>Marzec 2021

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Opis | Dostępność  |
|------------------------------|---------------|---------------|
| [Wyszukiwanie semantyczne](semantic-search-overview.md) | Kolekcja funkcji związanych z zapytaniami, które modyfikują wyniki wyszukiwania w celu uzyskania bardziej istotnych wyników. W przypadku małych zmian w żądaniu wyszukiwania można wypróbować te funkcje na istniejących indeksach.</br></br>[Zapytanie semantyczne](semantic-how-to-query-request.md) to nowy typ zapytania, który wykorzystuje zaawansowania w przetwarzaniu języka naturalnego w celu poprawy klasyfikacji, a także poznanie zamiaru zapytania, aby zapewnić odpowiedzi, napisy i semantykę semantyczną.</br></br>[Klasyfikacja i odpowiedzi semantyczne (odpowiedzi, napisy i najważniejsze)](semantic-how-to-query-response.md) odnoszą się do algorytmu, który definiuje zapytanie semantyczne i strukturę odpowiedzi. | Publiczna wersja zapoznawcza ([według żądania](https://aka.ms/SemanticSearchPreviewSignup)). </br></br>Użyj usługi [Search Documents (REST)](/rest/api/searchservice/preview-api/search-documents) API-Version = 2020-06 -30 — wersja zapoznawcza i [eksplorator wyszukiwania](search-explorer.md) w Azure Portal. </br></br>Obowiązują ograniczenia dotyczące regionów i warstw. |
| [Sprawdzanie pisowni — terminy zapytań](speller-how-to-add.md) | Przed przekazaniem terminów zapytania do aparatu wyszukiwania można sprawdzić, czy pisownia nie jest możliwa. `speller`Opcja działa z dowolnym typem kwerendy (prosty, pełny lub semantyczny). |  Publiczna wersja zapoznawcza, tylko REST, API-Version = 2020-06 -30 — wersja zapoznawcza|
| [Indeksator usługi SharePoint Online](search-howto-index-sharepoint-online.md) | Ten indeksator nawiązuje połączenie z witryną usługi SharePoint Online, dzięki czemu można indeksować zawartość z biblioteki dokumentów. | Publiczna wersja zapoznawcza, tylko REST, API-Version = 2020-06 -30 — wersja zapoznawcza |

## <a name="february-2021"></a>Luty 2021 r.

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Opis | Dostępność  |
|------------------------------|---------------|---------------|
| [Resetuj dokumenty (wersja zapoznawcza)](search-howto-run-reset-indexers.md) |  Przetwarza ponownie pojedyncze wybrane dokumenty wyszukiwania w obciążeniach indeksatora. | [Search 2020-06-30 API REST — wersja zapoznawcza](/rest/api/searchservice/index-preview) |
| [Strefy dostępności](search-performance-optimization.md#availability-zones)| Usługi wyszukiwania z co najmniej dwiema replikami w niektórych regionach, jak wymieniono w obszarze [skalowanie pod kątem wydajności, zapewnianie](search-performance-optimization.md#availability-zones)odporności przez posiadanie replik w dwóch lub większej liczbie różnych lokalizacji fizycznych.  | Aby określić dostępność, region i Data tworzenia usługi wyszukiwania. Szczegółowe informacje znajdują się w artykule skalowanie do wydajności. |
| [Interfejs wiersza polecenia platformy Azure](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | Nowe wersje zawierają teraz pełen zakres operacji w interfejsie API REST zarządzania 2020-08-01, w tym obsługę reguł zapory IP i prywatnego punktu końcowego. | Ogólnie dostępna. |

## <a name="january-2021"></a>Styczeń 2021 r.

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Opis | Dostępność  |
|------------------------------|-------------|---------------|
| [Akcelerator rozwiązań dla platformy Azure Wyszukiwanie poznawcze i QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Pobiera pytania i odpowiedzi z dokumentu i sugeruje najbardziej odpowiednie odpowiedzi. Aplikację demonstracyjną na żywo można znaleźć pod adresem [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) .  | Projekt Open-Source (bez umowy SLA) |

## <a name="2020-archive"></a>Archiwum 2020

| Month (Miesiąc) | Cecha | Opis |
|-------|---------|-------------|
| Listopad | [Szyfrowanie klucza zarządzanego przez klienta (rozszerzone)](search-security-manage-encryption-keys.md) | Rozszerza szyfrowanie zarządzane przez klienta przez cały zakres zasobów utworzonych i zarządzanych przez usługę wyszukiwania. Ogólnie dostępna.|
| Wrzesień | [Visual Studio Code rozszerzenie platformy Azure Wyszukiwanie poznawcze](search-get-started-vs-code.md) | Dodaje obszar roboczy, nawigację, IntelliSense i szablony służące do tworzenia indeksów, indeksatorów, źródeł danych i umiejętności. | Publiczna wersja zapoznawcza |
| Wrzesień | [Tożsamość usługi zarządzanej (indeksatory)](search-howto-managed-identities-data-sources.md) | Ogólnie dostępna.  |
| Wrzesień | [Żądania wychodzące przy użyciu linku prywatnego](search-indexer-howto-access-private.md) | Ogólnie dostępna.  |
| Wrzesień | [Interfejs API REST zarządzania (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Ogólnie dostępna. |
| Wrzesień | [Interfejs API REST zarządzania (2020-08-01 — wersja zapoznawcza)](/rest/api/searchmanagement/management-api-versions) | Dodaje udostępnione zasoby linku prywatnego dla baz danych Azure Functions i Azure SQL dla programu MySQL. |
| Wrzesień | [Zestaw .NET SDK 4,0](/dotnet/api/overview/azure/search/management) |  Aktualizacja zestawu Azure SDK dla zestawu SDK zarządzania, interfejs API REST w wersji 2020-08-01. Ogólnie dostępna.|
| Sierpień | [podwójne szyfrowanie](search-security-overview.md#encryption) | Ogólnie dostępna we wszystkich usługach wyszukiwania utworzonych po 1 sierpnia 2020 w następujących regionach: zachodnie stany USA 2, Wschodnie stany USA, Południowo-środkowe stany USA, US Gov Wirginia, US Gov Arizona. |
| Lipiec | [Azure.Search.DocBiblioteka kliencka uments](/dotnet/api/overview/azure/search.documents-readme) | Zestaw Azure SDK dla platformy .NET — ogólnie dostępny. |
| Lipiec | [azure.search.docBiblioteka kliencka uments](/python/api/overview/azure/search-documents-readme)  | Zestaw Azure SDK dla języka Python — ogólnie dostępny. |
| Lipiec | [@azure/search-documents Biblioteka kliencka](/javascript/api/overview/azure/search-documents-readme)  | Zestaw Azure SDK dla języka JavaScript, ogólnie dostępny. |
| Czerwiec | [Magazyn wiedzy](knowledge-store-concept-intro.md) | Ogólnie dostępna. |
| Czerwiec | [Interfejs API REST usługi Search 2020-06-30](/rest/api/searchservice/) | Ogólnie dostępna. |
| Czerwiec | [Search 2020-06-30 API REST — wersja zapoznawcza](/rest/api/searchservice/) | Dodaje Reset zestawu umiejętności do selektywnego ponownego przetwarzania umiejętności i przyrostowego wzbogacania. |
| Czerwiec | [Algorytm zgodności Okapi BM25](index-ranking-similarity.md) | Ogólnie dostępna. |
| Czerwiec |  **executionEnvironment** (dotyczy usług wyszukiwania przy użyciu prywatnego linku platformy Azure). | Ogólnie dostępna. |
| Czerwiec | [Umiejętność AML (wersja zapoznawcza)](cognitive-search-aml-skill.md) | Umiejętności poznawcze, które rozszerzają wzbogacenie AI z niestandardowym modelem Azure Machine Learning (AML). |
| Maj | [Sesje debugowania (wersja zapoznawcza)](cognitive-search-debug-session.md) | Debuger zestawu umiejętności w portalu.  |
| Maj | [Reguły adresów IP dla obsługi zapory w powiązaniu](service-configure-firewall.md) | Ogólnie dostępna.  |
| Maj | [Prywatny link do prywatnego punktu końcowego wyszukiwania](service-create-private-endpoint.md) | Ogólnie dostępna.  |
| Maj | [Tożsamość usługi zarządzanej (indeksatory) — (wersja zapoznawcza)](search-howto-managed-identities-data-sources.md) | Nawiązywanie połączenia ze źródłami danych platformy Azure przy użyciu tożsamości zarządzanej.  |
| Maj | [parametr zapytania SessionID](index-similarity-and-scoring.md), [scoringStatistics = Global](index-similarity-and-scoring.md#scoring-statistics)  | Globalne statystyki wyszukiwania, przydatne w przypadku [modeli uczenia maszynowego (LearnToRank) w celu wyszukania](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| Maj | [Ekspansja oceny przydatności funkcji Features (wersja zapoznawcza)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|Marzec  | [Natywne usuwanie nietrwałego obiektu BLOB (wersja zapoznawcza)](search-howto-index-changed-deleted-blobs.md) | Usuwa dokumenty wyszukiwania, Jeśli źródłowy obiekt BLOB jest usuwany z magazynu obiektów BLOB. |
|Marzec  | [Interfejs API REST zarządzania (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Ogólnie dostępna. |
|Luty | [Umiejętność wykrywania przez dane OSOBowe (wersja zapoznawcza)](cognitive-search-skill-pii-detection.md)  | Umiejętność, która wyodrębnia i maskuje dane osobowe. |
|Luty | [Umiejętność wyszukiwania jednostek niestandardowych (wersja zapoznawcza)](cognitive-search-skill-custom-entity-lookup.md) | Umiejętność, która umożliwia znalezienie słów i zwrotów z listy oraz etykiet wszystkich dokumentów ze zgodnymi jednostkami.  |
|January (Styczeń) | [Szyfrowanie klucza zarządzanego przez klienta](search-security-manage-encryption-keys.md) | Ogólnie dostępne  |
|January (Styczeń) | [Reguły adresów IP dla obsługiwanej zapory (wersja zapoznawcza)](service-configure-firewall.md) | Nowe właściwości **IpRule** i **NetworkRuleSet** w [interfejsie API metodę createorupdate](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service).  |
|January (Styczeń) | [Tworzenie prywatnego punktu końcowego (wersja zapoznawcza)](service-create-private-endpoint.md) | Skonfiguruj prywatny link do bezpiecznego połączenia z usługą wyszukiwania. Ta funkcja w wersji zapoznawczej ma zależność [prywatnego](../private-link/private-link-overview.md) i [Virtual Network platformy](../virtual-network/virtual-networks-overview.md) Azure w ramach rozwiązania. |

## <a name="2019-archive"></a>Archiwum 2019

| Month (Miesiąc) | Cecha | Opis |
|-------|---------|-------------|
|Grudzień | [Tworzenie aplikacji demonstracyjnej (wersja zapoznawcza)](search-create-app-portal.md) | Kreator generujący możliwy do pobrania plik HTML z funkcją Query (tylko do odczytu) do indeksu, który służy jako narzędzie do sprawdzania poprawności i testowania zamiast krótkiego obcinania do pełnej aplikacji klienckiej.|
|Listopad | [Wzbogacanie przyrostowe (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) | Buforuje przetwarzanie zestawu umiejętności do użytku w przyszłości.  |
|Listopad | [Umiejętność wyodrębniania dokumentów (wersja zapoznawcza)](cognitive-search-skill-document-extraction.md) | Umiejętność wyodrębniania zawartości pliku z zestawu umiejętności.|
|Listopad | [Umiejętność tłumaczenia tekstu](cognitive-search-skill-text-translation.md) | Umiejętności poznawcze używane podczas indeksowania, które oblicza i tłumaczy tekst. Ogólnie dostępna.|
|Listopad | [Szablony Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Szablon wizualizacji zawartości w sklepie z bazami danych |
|Listopad | [Azure Data Lake Storage Gen2 (wersja zapoznawcza)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (wersja zapoznawcza)](search-howto-index-cosmosdb.md)i [Cosmos DB interfejs API Cassandra (wersja zapoznawcza)](search-howto-index-cosmosdb.md) | Nowe źródła danych indeksatora w publicznej wersji zapoznawczej. |
|Lipiec | [Obsługa Azure Government w chmurze](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | Ogólnie dostępna.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nowa nazwa usługi

Zmieniono nazwę Azure Search na **platformę Azure wyszukiwanie poznawcze** w październiku 2019, aby odzwierciedlić rozwinięte (Nieopcjonalne) użycie umiejętności poznawczej i przetwarzania AI w operacjach podstawowych. Wersje interfejsu API, pakiety NuGet, przestrzenie nazw i punkty końcowe nie są zmieniane. Zmiany nazwy usługi nie wpływają na nowe i istniejące rozwiązania wyszukiwania.

## <a name="service-updates"></a>Aktualizacje usług

[Anonse aktualizacji usługi](https://azure.microsoft.com/updates/?product=search&status=all) dla systemu Azure wyszukiwanie poznawcze można znaleźć w witrynie sieci Web systemu Azure.