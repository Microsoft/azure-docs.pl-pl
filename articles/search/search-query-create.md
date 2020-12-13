---
title: Tworzenie podstawowego zapytania
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć żądanie zapytania w Wyszukiwanie poznawcze, które narzędzia i interfejsy API do użycia na potrzeby testowania i kodu oraz jak decyzje dotyczące zapytań zaczynają się od projektu indeksu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371177"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>Tworzenie podstawowego zapytania w usłudze Azure Wyszukiwanie poznawcze

W tym artykule opisano konstruowanie zapytań krok po kroku. Przykłady są w stanie REST, aby można było kopiować ciągi do **Eksploratora wyszukiwania** w portalu lub interaktywnie kompilować zapytania przy użyciu programu Poster lub Visual Studio Code. W przykładach w tym artykule można użyć dowolnej warstwy lub wersji Wyszukiwanie poznawcze.

## <a name="choose-a-tool-or-api"></a>Wybierz narzędzie lub interfejs API

Wybierz spośród następujących narzędzi i interfejsów API, aby utworzyć zapytania do testowania lub obciążeń produkcyjnych.

| Metodologia | Opis |
|-------------|-------------|
| Portal| [Eksplorator wyszukiwania (Portal)](search-explorer.md) udostępnia pasek wyszukiwania i opcje dotyczące opcji indeks i wersja interfejsu API. Wyniki są zwracane jako dokumenty JSON. Zalecane do wczesnego badania, testowania i weryfikacji. <br/>[Dowiedz się więcej.](search-explorer.md) |
| Narzędzia do testowania sieci Web| W przypadku zadawania [dokumentów wyszukiwania](/rest/api/searchservice/search-documents) w usłudze [posters lub Visual Studio Code](search-get-started-rest.md) są dostępne silne wybory. Interfejs API REST obsługuje wszystkie operacje programistyczne w usłudze Azure Wyszukiwanie poznawcze, dzięki czemu można wystawiać żądania interaktywnie, aby zrozumieć, jak to działa przed inwestycjami w kod.  |
| Zestaw Azure SDK | [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) może służyć do wykonywania zapytań względem indeksu wyszukiwania w języku C#.  [Dowiedz się więcej.](search-howto-dotnet-sdk.md) <br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) może służyć do wykonywania zapytań względem indeksu wyszukiwania w języku Python. [Dowiedz się więcej.](search-get-started-python.md) <br/><br/> [SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) może służyć do wykonywania zapytań względem indeksu wyszukiwania w języku JavaScript. [Dowiedz się więcej.](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>Konfigurowanie klienta wyszukiwania

Klient wyszukiwania jest uwierzytelniany w usłudze wyszukiwania, wysyła żądania i obsługuje odpowiedzi. Zapytania są zawsze kierowane do kolekcji dokumentów o pojedynczym indeksie. Nie można przyłączyć indeksów ani tworzyć niestandardowych lub tymczasowych struktur danych jako obiektów docelowych zapytań.

### <a name="in-the-portal"></a>W portalu

Eksplorator wyszukiwania i inne narzędzia portalu mają wbudowane połączenie z usługą za pomocą bezpośrednich indeksów dostępu i innych obiektów ze stron portalu. Dostęp do narzędzi, kreatorów i obiektów założono, że masz prawa administracyjne do usługi. Za pomocą Eksploratora wyszukiwania możesz skupić się na określeniu ciągu wyszukiwania i innych parametrów. 

### <a name="using-rest"></a>Korzystanie z interfejsu REST

W przypadku wywołań REST można użyć programu [Poster lub podobnych narzędzi](search-get-started-rest.md) jako klienta, aby określić żądanie [przeszukiwania dokumentów](/rest/api/searchservice/search-documents) . Każde żądanie jest autonomiczne, więc należy podać punkt końcowy (adres URL do usługi) oraz administrator lub klucz interfejsu API zapytań, aby uzyskać dostęp. W zależności od żądania adres URL może zawierać również nazwę indeksu, kolekcję dokumentów i inne właściwości. W nagłówku żądania są przesyłane kilka właściwości, takich jak typ zawartości i klucz interfejsu API. Inne parametry można przesłać na adres URL lub w treści żądania. Wszystkie wywołania REST wymagają klucza interfejsu API na potrzeby uwierzytelniania oraz interfejsu API-Version.

### <a name="using-azure-sdks"></a>Korzystanie z zestawów SDK platformy Azure

Zestawy Azure SDK zapewniają klientom wyszukiwania, którzy mogą utrzymywać stan utrwalania, umożliwiając ponowne użycie połączenia. Dla operacji zapytania można utworzyć wystąpienie SearchClient i podać wartości dla następujących właściwości: punkt końcowy, klucz, indeks. Następnie można wywołać metodę wyszukiwania, aby podać ciąg zapytania. 

| Język | Klient | Przykład |
|----------|--------|---------|
| C# i .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Wyślij pierwsze zapytanie wyszukiwania w języku C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Wyślij pierwsze zapytanie wyszukiwania w języku Python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Wyślij pierwsze zapytanie wyszukiwania w języku Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Wyślij pierwsze zapytanie wyszukiwania w języku JavaScript](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Wybierz parser: prosty | szczegółowe

Usługa Azure Wyszukiwanie poznawcze umożliwia wybór między dwoma parserami zapytań do obsługi typowych i wyspecjalizowanych zapytań. Żądania przy użyciu prostego analizatora są zwykle zapytania wyszukiwania pełnotekstowego, formułowane przy użyciu [prostej składni zapytania](query-simple-syntax.md), wybrane jako domyślne dla jego szybkości i skuteczności w bezpłatnych zapytaniach tekstowych. Ta składnia obsługuje wiele typowych operatorów wyszukiwania, w tym operatory AND, OR, NOT, phrase, sufiks i pierwszeństwo.

[Pełna składnia zapytań Lucene](query-Lucene-syntax.md#bkmk_syntax), którą można włączyć po dodaniu `queryType=full` żądania, uwidacznia powszechnie przyjęty i wyraźny język zapytań opracowany w ramach oprogramowania [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Pełna składnia rozszerza prostą składnię. Wszystkie zapytania zapisane dla prostej składni są uruchamiane w ramach pełnego analizatora Lucene. 

Poniższe przykłady ilustrują punkt: to samo zapytanie, ale z różnymi **`queryType`** ustawieniami, które dają różne wyniki. W pierwszym zapytaniu polecenie `^3` After `historic` jest traktowane jako część wyszukiwanego terminu. Górny wynik tego zapytania to "Marquis plac & Suites", który ma *Ocean* w opisie.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

To samo zapytanie używające pełnego analizatora Lucene interpretuje `^3` jako Detonator długoterminowy jako pole. Przełączenie analizatorów zmienia rangę, z wynikami zawierającymi termin *historyczny* w górnej części.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>Włączanie zachowań zapytania w indeksie

Projekt indeksu i projekt zapytania są ściśle powiązane z platformą Azure Wyszukiwanie poznawcze. *Schemat indeksu* z atrybutami każdego pola określa rodzaj zapytania, które można skompilować.

Atrybuty indeksu w polu ustawiają dozwolone operacje — czy pole można *wyszukiwać* w indeksie, *pobrać* z wyników, do *sortowania*, *filtrować* i tak dalej. W przykładowych zapytaniach `"$orderby": "Rating desc"` działa tylko, ponieważ pole Rating jest oznaczone jako do *sortowania* w schemacie indeksu.

![Definicja indeksu dla przykładu hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definicja indeksu dla przykładu hotelu")

Powyższy zrzut ekranu jest częściową listą atrybutów indeksu dla [przykładowego indeksu hoteli](search-get-started-portal.md). Można utworzyć i wyświetlić cały schemat indeksu w portalu. Aby uzyskać więcej informacji na temat atrybutów indeksu, zobacz [Tworzenie indeksu (interfejs API REST)](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Następne kroki

Teraz, po zrozumieniu sposobu tworzenia żądania, wypróbuj przykłady przy użyciu prostej i pełnej składni.

+ [Przykłady prostych zapytań](search-query-simple-examples.md)
+ [Przykłady zapytań składni Lucene w celu tworzenia zaawansowanych zapytań](search-query-lucene-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)