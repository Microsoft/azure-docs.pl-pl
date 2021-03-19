---
title: Importowanie danych do indeksu wyszukiwania przy użyciu Azure Portal
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak za pomocą Kreatora importu danych w Azure Portal przeszukiwać dane platformy Azure z Cosmos DB, BLOB Storage, Table Storage, SQL Database, wystąpienia zarządzanego SQL i SQL Server na maszynach wirtualnych platformy Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cff009d5d1e187e8d0330fadca530b57b3e3d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935215"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Kreator importu danych dla platformy Azure Wyszukiwanie poznawcze

Azure Portal zawiera kreatora **importowania danych** na pulpicie nawigacyjnym usługi Azure wyszukiwanie poznawcze do tworzenia prototypów i ładowania indeksu. W tym artykule opisano zalety i ograniczenia dotyczące używania Kreatora, wejść i wyjść oraz niektóre informacje o użyciu. Aby zapoznać się z przewodnikiem krok po kroku w Kreatorze przy użyciu wbudowanych danych przykładowych, zobacz [Tworzenie indeksu wyszukiwanie poznawcze platformy Azure przy użyciu Azure Portal](search-get-started-portal.md) przewodnika Szybki Start.

Operacje wykonywane przez kreatora obejmują:

1 — nawiązywanie połączenia z obsługiwanym źródłem danych platformy Azure.

2 — Tworzenie schematu indeksu, wywnioskowanego przez próbkowanie danych źródłowych.

3 — opcjonalnie Dodaj wzbogacenia AI, aby wyodrębnić lub wygenerować zawartość i strukturę.

4 — Uruchom kreatora, aby utworzyć obiekty, zaimportować dane, ustawić harmonogram i inne opcje konfiguracji.

Kreator wyprowadza wiele obiektów, które są zapisane w usłudze wyszukiwania, do których można uzyskać dostęp programowo lub w innych narzędziach.

## <a name="advantages-and-limitations"></a>Zalety i ograniczenia

Przed zapisaniem dowolnego kodu można użyć kreatora do tworzenia prototypów i testowania koncepcji. Kreator łączy się z zewnętrznymi źródłami danych, próbuje utworzyć początkowy indeks, a następnie importuje dane jako dokumenty JSON do indeksu na platformie Azure Wyszukiwanie poznawcze. 

Próbkowanie to proces, za pomocą którego jest wywnioskowany schemat indeksu i ma pewne ograniczenia. Po utworzeniu źródła danych Kreator wybiera przykład dokumentów, aby określić, które kolumny są częścią źródła danych. Nie wszystkie pliki są odczytywane, ponieważ może to potrwać kilka godzin w przypadku bardzo dużych źródeł danych. Uwzględniając wybór dokumentów, metadane źródłowe, takie jak nazwa pola lub typ, są używane do tworzenia kolekcji pól w schemacie indeksu. W zależności od złożoności danych źródłowych może być konieczne zmodyfikowanie początkowego schematu pod kątem dokładności lub zwiększenie jego kompletności. Możesz wprowadzić zmiany w tekście na stronie definicji indeksu.

Ogólnie, zalety korzystania z kreatora są jasne: tak długo, jak są spełnione wymagania, można prototypować indeks Queryable w ciągu kilku minut. Niektóre złożoności indeksowania, takie jak dostarczanie danych jako dokumenty JSON, są obsługiwane przez kreatora.

Znane ograniczenia są podsumowywane w następujący sposób:

+ Kreator nie obsługuje iteracji ani ponownego użycia. Każda z nich przechodzi przez kreatora tworzy nowy indeks, zestawu umiejętności i konfigurację indeksatora. Tylko źródła danych mogą być utrwalane i ponownie używane w kreatorze. Aby edytować lub udoskonalać inne obiekty, należy użyć interfejsów API REST lub zestawu SDK platformy .NET do pobrania i zmodyfikowania struktur.

+ Zawartość źródłowa musi znajdować się w obsługiwanym źródle danych platformy Azure.

+ Próbkowanie znajduje się w przedziale danych źródłowych. W przypadku dużych źródeł danych jest możliwe, aby Kreator mógł pominąć pola. Może być konieczne rozbudowa schematu lub poprawienie wywnioskowanych typów danych, jeśli próbkowanie jest niewystarczające.

+ Wzbogacanie AI, które jest dostępne w portalu, jest ograniczone do kilku wbudowanych umiejętności. 

+ [Magazyn wiedzy](knowledge-store-concept-intro.md), który można utworzyć za pomocą kreatora, jest ograniczony do kilku domyślnych projekcji. Jeśli chcesz zapisać wzbogacone dokumenty utworzone przez kreatora, kontener obiektów blob i tabele są dostarczane z domyślnymi nazwami i strukturą.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Dane wejściowe źródła danych

Kreator **importu danych** łączy się z zewnętrznym źródłem danych przy użyciu wewnętrznej logiki zapewnianej przez indeksatory usługi Azure wyszukiwanie poznawcze, które są wyposażone w przykładowe źródło, odczyt metadanych, dokumenty z krakingów, odczytywanie zawartości i struktury oraz Serializowanie zawartości w formacie JSON na potrzeby późniejszego importowania do usługi Azure wyszukiwanie poznawcze.

Można importować tylko z pojedynczej tabeli, widoku bazy danych lub równoważnej struktury danych, jednak struktura może zawierać hierarchiczne lub zagnieżdżone struktury podstruktur. Aby uzyskać więcej informacji, zobacz [jak modelować typy złożone](search-howto-complex-data-types.md).

Przed uruchomieniem kreatora należy utworzyć pojedynczą tabelę lub widok i musi on zawierać zawartość. Z oczywistych powodów nie ma sensu uruchamiania kreatora **importowania danych** w pustym źródle danych.

|  Zaznaczenie | Opis |
| ---------- | ----------- |
| **Istniejące źródło danych** |Jeśli masz już zdefiniowane indeksatory w usłudze wyszukiwania, być może masz istniejącą definicję źródła danych, której można użyć ponownie. Na platformie Azure Wyszukiwanie poznawcze obiekty źródła danych są używane tylko przez indeksatory. Obiekt źródła danych można utworzyć programowo lub za pomocą kreatora **importu danych** i ponownie użyć ich w razie konieczności.|
| **Samples**| Usługa Azure Wyszukiwanie poznawcze oferuje dwa wbudowane przykładowe źródła danych, które są używane w samouczkach i przewodnikach szybki start: w przypadku bazy danych SQL w wersji rzeczywistej i w hotelach, która jest hostowana na Cosmos DB. Aby zapoznać się z przykładem dotyczącym hoteli, zobacz [Tworzenie indeksu w Azure Portal](search-get-started-portal.md) Szybki Start. |
| [**Wystąpienie zarządzane Azure SQL Database lub SQL**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Nazwę usługi, poświadczenia użytkownika z uprawnieniem do odczytu bazy danych i nazwę bazy danych można określić na stronie lub przy użyciu parametrów połączenia ADO.NET. Wybierz opcję parametrów połączenia, aby wyświetlić lub dostosować właściwości. <br/><br/>Na stronie należy określić tabelę lub widok zawierające zestaw wierszy. Ta opcja jest dostępna po udanym nawiązaniu połączenia. Pojawia się wtedy lista rozwijana, z której można dokonać wyboru.|
| **Program SQL Server na maszynie wirtualnej platformy Azure** |Określ w pełni kwalifikowaną nazwę usługi, identyfikator użytkownika i hasło oraz bazę danych jako parametry połączenia. Aby użyć tego źródła danych, należy wcześniej zainstalować w magazynie lokalnym certyfikat szyfrujący połączenie. Aby uzyskać instrukcje, zobacz [połączenie maszyny wirtualnej SQL z platformą Azure wyszukiwanie poznawcze](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Na stronie należy określić tabelę lub widok zawierające zestaw wierszy. Ta opcja jest dostępna po udanym nawiązaniu połączenia. Pojawia się wtedy lista rozwijana, z której można dokonać wyboru. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Wymagane jest konto, baza danych i kolekcja. Wszystkie dokumenty w kolekcji zostaną uwzględnione w indeksie. Można zdefiniować zapytanie, aby spłaszczyć lub filtrować zestaw wierszy, lub pozostawić zapytanie puste. Zapytanie nie jest wymagane w tym kreatorze.|
| [**Blob Storage platformy Azure**](search-howto-indexing-azure-blob-storage.md) |Wymagane jest miedzy innymi konto magazynu i kontener. Opcjonalnie, jeśli nazwa obiektu blob jest zgodna z konwencją nazw wirtualnych do celów grupowania, można określić część nazwy oznaczającą katalog wirtualny jako folder w kontenerze. Więcej informacji zawiera artykuł [Indexing Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie w usłudze Blob Storage). |
| [**Table Storage platformy Azure**](search-howto-indexing-azure-tables.md) |Wymagane jest miedzy innymi konto magazynu i nazwa tabeli. Opcjonalnie można określić zapytanie w celu pobrania podzbioru tabel. Więcej informacji zawiera artykuł [Indexing Table Storage](search-howto-indexing-azure-tables.md) (Indeksowanie w usłudze Table Storage). |

## <a name="wizard-output"></a>Dane wyjściowe kreatora

W tle Kreator tworzy, konfiguruje i wywołuje następujące obiekty. Po uruchomieniu kreatora można znaleźć jego dane wyjściowe na stronach portalu. Na stronie Przegląd usługi znajdują się listy indeksów, indeksatorów, źródeł danych i umiejętności. Definicje indeksów można wyświetlać w pełnym formacie JSON w portalu. W przypadku innych definicji można użyć [interfejsu API REST](/rest/api/searchservice/) w celu uzyskania określonych obiektów.

| Obiekt | Opis | 
|--------|-------------|
| [Źródło danych](/rest/api/searchservice/create-data-source)  | Utrzymuje informacje o połączeniu z danymi źródłowymi, w tym poświadczeniami. Obiekt źródła danych jest używany wyłącznie z indeksatorami. | 
| [Indeks](/rest/api/searchservice/create-index) | Fizyczna struktura danych używana do wyszukiwania pełnotekstowego i innych zapytań. | 
| [Zestawu umiejętności](/rest/api/searchservice/create-skillset) | Pełny zestaw instrukcji dotyczących manipulowania, przekształcania i kształtowania zawartości, w tym analizowania i wyodrębniania informacji z plików obrazów. Z wyjątkiem bardzo prostych i ograniczonych struktur, zawiera odwołanie do zasobu Cognitive Services, który zapewnia wzbogacanie. Opcjonalnie może również zawierać definicję sklepu z wiedzą.  | 
| [Indeksator](/rest/api/searchservice/create-indexer)  | Obiekt konfiguracji, określający źródło danych, indeks docelowy, opcjonalny zestawu umiejętności, opcjonalny harmonogram i opcjonalne ustawienia konfiguracji, które dotyczą błędów i kodowania Base-64. |


## <a name="how-to-start-the-wizard"></a>Jak uruchomić Kreatora

Kreator importu danych jest uruchamiany z paska poleceń na stronie Przegląd usługi.

1. W [Azure Portal](https://portal.azure.com)Otwórz stronę usługi wyszukiwania na pulpicie nawigacyjnym lub [Znajdź usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na liście usług.

2. Na stronie Przegląd usługi u góry kliknij pozycję **Importuj dane**.

   ![Importuj dane — polecenie w portalu](./media/search-import-data-portal/import-data-cmd2.png "Uruchamianie kreatora importu danych")

Możesz również uruchomić **Importowanie danych** z innych usług platformy Azure, w tym Azure Cosmos DB, Azure SQL Database, wystąpienia zarządzanego SQL i usługi Azure Blob Storage. Znajdź przycisk **dodaj wyszukiwanie poznawcze platformy Azure** w okienku nawigacji po lewej stronie Przegląd usługi.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Jak edytować lub zakończyć schemat indeksu w Kreatorze

Kreator generuje niekompletny indeks, który zostanie wypełniony dokumentami uzyskanymi ze źródła danych wejściowych. W przypadku indeksów funkcjonalnych upewnij się, że masz zdefiniowane następujące elementy.

1. Czy lista pól jest kompletna? Dodaj nowe pola, które nie zostały pominięte, i Usuń wszystkie, które nie dodają wartości do środowiska wyszukiwania lub nie będą używane w [wyrażeniu filtru](search-query-odata-filter.md) lub [profilu oceniania](index-add-scoring-profiles.md).

1. Czy dane są odpowiednie dla danych przychodzących? Usługa Azure Wyszukiwanie poznawcze obsługuje [typy danych Entity Data Model (EDM)](/rest/api/searchservice/supported-data-types). W przypadku danych SQL Azure istnieje [Mapowanie wykresu](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) , który określa równoważne wartości. Aby uzyskać więcej informacji, zobacz [mapowania pól i przekształcenia](search-indexer-field-mappings.md).

1. Czy masz jedno pole, które może być *kluczem*? To pole musi mieć wartość EDM. String i musi jednoznacznie identyfikować dokument. Dane relacyjne mogą być mapowane na klucz podstawowy. W przypadku obiektów BLOB może to być `metadata-storage-path` . Jeśli wartości pola zawierają spacje lub kreski, należy ustawić opcję **kodowania Base-64** w kroku **Utwórz indeksator** w obszarze **Opcje zaawansowane**, aby pominąć sprawdzanie poprawności tych znaków.

1. Ustaw atrybuty, aby określić, jak to pole jest używane w indeksie. 

   Wykonaj ten krok, ponieważ atrybuty określają fizyczne wyrażenie pól w indeksie. Aby później zmienić atrybuty, należy nawet programowo porzucić i ponownie skompilować indeks. Podstawowe atrybuty,  takie jak wyszukiwanie **i pobieranie** , mają [niewielki wpływ na magazyn](search-what-is-an-index.md#index-size). Włączenie filtrów i użycie sugestii zwiększa wymagania dotyczące magazynu. 
   
   + **Wyszukiwanie** umożliwia wyszukiwanie pełnotekstowe. Każde pole używane w kwerendach w dowolnej postaci lub w wyrażeniach zapytań musi mieć ten atrybut. Odwrócone indeksy są tworzone dla każdego pola, które jest oznaczone jako możliwe do **przeszukania**.

   + Możliwość **pobierania** zwraca pole w wynikach wyszukiwania. Każde pole, które zapewnia zawartość do wyników wyszukiwania, musi mieć ten atrybut. Ustawienie tego pola nie ma znaczącego wpływu na rozmiar indeksu.

   + **Filtrowanie** umożliwia odwoływanie się do pola w wyrażeniach filtru. Każde pole używane w wyrażeniu **$Filter**  musi mieć ten atrybut. Wyrażenia filtru są dla dokładnych dopasowań. Ponieważ ciągi tekstowe pozostają nienaruszone, do obsługi zawartości Verbatim jest wymagany dodatkowy magazyn.

   + Element **kroju** umożliwia pole dla nawigacji aspektowej. Tylko pola oznaczone jako możliwe do **filtrowania** mogą być oznaczone jako **kroju**.

   + **Sortowanie umożliwia używanie** pola w sortowaniu. Każde pole używane w wyrażeniu **$OrderBy** musi mieć ten atrybut.

1. Potrzebujesz [analizy leksykalnej](search-lucene-query-architecture.md#stage-2-lexical-analysis)? W przypadku pól EDM. String, które są **przeszukiwane**, można ustawić **Analizator** , jeśli chcesz, aby ulepszone językowe indeksowania i zapytania. 

   Wartość domyślna to *standardowa Lucene* , ale można wybrać *język angielski firmy Microsoft* , jeśli chcesz użyć analizatora firmy Microsoft do zaawansowanego przetwarzania leksykalnego, takiego jak rozwiązywanie nietypowych form rzeczowników i czasowników. W portalu można określić tylko analizatory języka. Przy użyciu analizatora niestandardowego lub analizatora nieobsługującego języka, takiego jak słowo kluczowe, wzorzec i tak dalej, należy wykonać programowo. Aby uzyskać więcej informacji na temat analizatorów, zobacz [Dodawanie analizatorów języka](search-language-support.md).

1. Potrzebujesz funkcji typeahead w formie autouzupełniania lub sugerowanych wyników? Zaznacz pole wyboru **Sugeruj** , aby włączyć [sugestie zapytania typeahead oraz Autouzupełnianie](index-add-suggesters.md) dla wybranych pól. Sugestie dodają do liczby tokenów w indeksie i w ten sposób zużywają więcej miejsca do magazynowania.


## <a name="next-steps"></a>Następne kroki

Najlepszym sposobem na zrozumienie korzyści i ograniczeń kreatora jest przechodzenie do niego. Poniższy przewodnik Szybki Start przeprowadzi Cię przez poszczególne kroki.

> [!div class="nextstepaction"]
> [Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu Azure Portal](search-get-started-portal.md)