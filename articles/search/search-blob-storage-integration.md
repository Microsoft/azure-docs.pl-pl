---
title: Wyszukaj zawartość usługi Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Dowiedz się więcej o wyodrębnianiu tekstu z obiektów blob platformy Azure i przeszukiwaniu tekstu pełnotekstowego w indeksie Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f61bf635cc61a2153a7bb016ef4b4711d7ba7391
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355299"
---
# <a name="search-over-azure-blob-storage-content"></a>Wyszukaj zawartość usługi Azure Blob Storage

Wyszukiwanie w różnych typach zawartości przechowywanych w usłudze Azure Blob Storage może być trudnym problemem do rozwiązania. W tym artykule zapoznaj się z podstawowym przepływem pracy dotyczącym wyodrębniania zawartości i metadanych z obiektów blob i wysyłania jej do indeksu wyszukiwania w usłudze Azure Wyszukiwanie poznawcze. Indeks otrzymany można zbadać przy użyciu wyszukiwania pełnotekstowego.

> [!NOTE]
> Znasz już przepływ pracy i kompozycję? [Jak skonfigurować indeksator obiektów BLOB](search-howto-indexing-azure-blob-storage.md) .

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Co oznacza Dodawanie wyszukiwania pełnotekstowego do danych obiektów BLOB

Azure Wyszukiwanie poznawcze to usługa wyszukiwania obsługująca obciążenia indeksowania i zapytań w przypadku indeksów zdefiniowanych przez użytkownika, które zawierają zawartość, którą można przeszukiwać zdalnie hostowaną w chmurze. Współlokalizowanie zawartości z możliwością wyszukiwania za pomocą aparatu zapytań jest niezbędne w celu zapewnienia wydajności, zwracając wyniki w szybkości od zapytań wyszukiwania.

Wyszukiwanie poznawcze integruje się z usługą Azure Blob Storage w warstwie indeksowania, importując zawartość obiektu BLOB jako dokumenty wyszukiwania indeksowane w *odwróconych indeksach* i innych strukturach zapytań, które obsługują kwerendy tekstowe w postaci nieodpłatnej i wyrażenia filtru. Ponieważ zawartość obiektu BLOB jest indeksowana do indeksu wyszukiwania, możesz użyć pełnego zakresu funkcji zapytania w usłudze Azure Wyszukiwanie poznawcze, aby znaleźć informacje o zawartości obiektu BLOB.

Dane wejściowe są obiektami BLOB w jednym kontenerze w usłudze Azure Blob Storage. Obiekty blob mogą być niemal dowolnym rodzajem danych tekstowych. Jeśli obiekty blob zawierają obrazy, można dodać [wzbogacanie AI do indeksowania obiektów BLOB](search-blob-ai-integration.md) w celu utworzenia i wyodrębnienia tekstu z obrazów.

Dane wyjściowe są zawsze indeksem Wyszukiwanie poznawcze platformy Azure, używanym do szybkiego wyszukiwania tekstu, pobierania i eksploracji w aplikacjach klienckich. W obszarze między jest architekturą potoku indeksowania. Potok jest oparty na funkcji *indeksatora* omówionej w dalszej sekcji tego artykułu.

Po utworzeniu i wypełnieniu indeksu istnieje on niezależnie od kontenera obiektów blob, ale można ponownie uruchomić operacje indeksowania, aby odświeżyć indeks na podstawie zmienionych dokumentów. Informacje o znacznikach czasu dla poszczególnych obiektów BLOB są używane na potrzeby wykrywania zmian. Można wybrać opcję zaplanowanego wykonania lub indeksowania na żądanie jako mechanizmu odświeżania.

## <a name="required-resources"></a>Wymagane zasoby

Potrzebujesz zarówno usługi Azure Wyszukiwanie poznawcze, jak i usługi Azure Blob Storage. W magazynie obiektów BLOB wymagany jest kontener, który udostępnia zawartość źródłową.

Możesz rozpocząć bezpośrednio na stronie portalu konta magazynu. Na lewej stronie nawigacyjnej w obszarze **BLOB Service** kliknij pozycję **Dodaj wyszukiwanie poznawcze platformy Azure** , aby utworzyć nową usługę, lub wybierz istniejącą. 

Po dodaniu Wyszukiwanie poznawcze platformy Azure do konta magazynu można wykonać standardowy proces, aby zindeksować dane obiektów BLOB. Zalecamy użycie kreatora **importu danych** na platformie Azure wyszukiwanie poznawcze w celu ułatwienia wstępnego wprowadzenia lub wywołania interfejsów API REST za pomocą narzędzia takiego jak program Poster. Ten samouczek przeprowadzi Cię przez kroki wywoływania interfejsu API REST w programie Poster: [indeksowanie i wyszukiwanie danych częściowo strukturalnych (obiektów BLOB JSON) w usłudze Azure wyszukiwanie poznawcze](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Używanie indeksatora obiektów BLOB

*Indeksator* to podusługa z uwzględnieniem źródła danych w wyszukiwanie poznawcze, wyposażona w wewnętrzną logikę do próbkowania danych, odczytywanie danych metadanych, pobieranie danych i Serializowanie danych z formatów natywnych do dokumentów JSON w celu późniejszego zaimportowania. 

Obiekty blob w usłudze Azure Storage są indeksowane przy użyciu [indeksatora usługi azure wyszukiwanie poznawcze BLOB Storage](search-howto-indexing-azure-blob-storage.md). Można wywołać ten indeksator przy użyciu kreatora **importu danych** , interfejsu API REST lub zestawu .NET SDK. W kodzie, używasz tego indeksatora, ustawiając typ i dostarczając informacje o połączeniu, które obejmują konto usługi Azure Storage wraz z kontenerem obiektów BLOB. Obiekty blob można podzbiórować przez utworzenie katalogu wirtualnego, który można następnie przekazać jako parametr lub przez filtrowanie według rozszerzenia typu pliku.

Indeksator wykonuje "pęknięcia dokumentu", otwierając obiekt BLOB w celu przeprowadzenia inspekcji zawartości. Po nawiązaniu połączenia ze źródłem danych jest to pierwszy krok w potoku. W przypadku danych obiektów BLOB są wykrywane pliki PDF, Office docs i inne typy zawartości. Łamanie dokumentów przy użyciu wyodrębniania tekstu nie jest naliczane. Jeśli obiekty blob zawierają zawartość obrazu, obrazy są ignorowane, chyba że [dodasz wzbogacanie AI](search-blob-ai-integration.md). Standardowe indeksowanie dotyczy tylko zawartości tekstowej.

Indeksator obiektów BLOB zawiera parametry konfiguracji i obsługuje śledzenie zmian, jeśli dane podstawowe zawierają wystarczające informacje. Aby dowiedzieć się więcej na temat podstawowych funkcji w [usłudze Azure wyszukiwanie poznawcze indeksator magazynu obiektów BLOB](search-howto-indexing-azure-blob-storage.md), należy zapoznać się z tematem.

### <a name="supported-content-types"></a>Obsługiwane typy zawartości

Uruchamiając indeksator obiektów BLOB w kontenerze, można wyodrębnić tekst i metadane z następujących typów zawartości za pomocą pojedynczego zapytania:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indeksowanie metadanych obiektu BLOB

Typowym scenariuszem, który ułatwia sortowanie za pomocą obiektów BLOB dowolnego typu zawartości, jest indeksowanie zarówno niestandardowych metadanych, jak i właściwości systemu dla każdego obiektu BLOB. W ten sposób informacje o wszystkich obiektach Blob są indeksowane niezależnie od typu dokumentu, przechowywane w indeksie usługi wyszukiwania. Korzystając z nowego indeksu, można następnie posortować, filtrować i aspektować zawartość całej zawartości magazynu obiektów BLOB.

> [!NOTE]
> Tagi indeksów obiektów BLOB są natywnie indeksowane przez usługę BLOB Storage i udostępniane na potrzeby wykonywania zapytań. Jeśli atrybuty klucza/wartości obiektów BLOB wymagają funkcji indeksowania i filtrowania, zamiast metadanych należy użyć tagów indeksów obiektów BLOB.
>
> Aby dowiedzieć się więcej na temat indeksu obiektów blob, zobacz temat [Zarządzanie danymi i znajdowanie ich w usłudze Azure Blob Storage przy użyciu indeksu obiektów BLOB](../storage/blobs/storage-manage-find-blobs.md).

### <a name="indexing-json-blobs"></a>Indeksowanie obiektów BLOB JSON

Indeksatory można skonfigurować do wyodrębniania zawartości strukturalnej znalezionej w obiektach Blob, które zawierają kod JSON. Indeksator może odczytywać obiekty blob JSON i analizować zawartość strukturalną do odpowiednich pól dokumentu wyszukiwania. Indeksatory mogą również przyjmować obiekty blob, które zawierają tablicę obiektów JSON i mapują każdy element do oddzielnego dokumentu wyszukiwania. Można ustawić tryb analizowania, który ma wpływ na typ obiektu JSON utworzonego przez indeksator.

## <a name="search-blob-content-in-a-search-index"></a>Wyszukaj zawartość obiektu BLOB w indeksie wyszukiwania 

Dane wyjściowe indeksatora są indeksem wyszukiwania używanym do interaktywnej eksploracji przy użyciu dowolnego tekstu i filtrowanych zapytań w aplikacji klienckiej. W przypadku początkowej eksploracji i weryfikacji zawartości zalecamy rozpoczęcie pracy z [Eksploratorem wyszukiwania](search-explorer.md) w portalu, aby sprawdzić strukturę dokumentu. Możesz użyć [prostej składni zapytania](query-simple-syntax.md), [pełnej składni zapytania](query-lucene-syntax.md)i [składni wyrażenia filtru](query-odata-filter-orderby-syntax.md) w Eksploratorze wyszukiwania.

Bardziej trwałe rozwiązanie polega na zebraniu danych wejściowych zapytania i zaprezentowaniu odpowiedzi jako wyników wyszukiwania w aplikacji klienckiej. W poniższym samouczku w języku C# opisano sposób tworzenia aplikacji wyszukiwania: [Tworzenie pierwszej aplikacji na platformie Azure wyszukiwanie poznawcze](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Następne kroki

+ [Przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą Azure Portal (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Konfigurowanie indeksatora obiektów BLOB (Azure Wyszukiwanie poznawcze)](search-howto-indexing-azure-blob-storage.md)