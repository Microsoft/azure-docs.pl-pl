---
title: Możliwości wielu modeli
description: Azure SQL Database umożliwia współpracę z wieloma modelami danych w tej samej bazie danych.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73802814"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Możliwości wielomodelowe Azure SQL Database

Wielomodelowe bazy danych umożliwiają przechowywanie i współpracują z danymi przedstawionymi w wielu formatach danych, takimi jak dane relacyjne, wykresy, dokumenty JSON/XML, pary klucz-wartość itp.

## <a name="when-to-use-multi-model-capabilities"></a>Kiedy używać funkcji wielomodelowych

Azure SQL Database jest zaprojektowana do pracy z modelem relacyjnym, który zapewnia najlepszą wydajność w większości przypadków w przypadku różnych aplikacji ogólnego przeznaczenia. Jednak Azure SQL Database nie jest ograniczony tylko do danych relacyjnych. Azure SQL Database umożliwia korzystanie z różnych formatów nierelacyjnych, które są ściśle zintegrowane z modelem relacyjnym.
Należy rozważyć użycie funkcji wielomodelowych Azure SQL Database w następujących przypadkach:
- Masz pewne informacje i struktury, które lepiej pasują do modeli NoSQL i nie chcesz używać oddzielnej bazy danych NoSQL.
- Większość danych jest odpowiednia dla modelu relacyjnego i należy modelować niektóre części danych w stylu NoSQL.
- Chcesz korzystać z zaawansowanego języka Transact-SQL do wykonywania zapytań i analizowania danych relacyjnych i NoSQL oraz integrowania ich z różnymi narzędziami i aplikacjami, które mogą korzystać z języka SQL.
- Aby zastosować funkcje bazy danych, takie jak [technologie w pamięci](sql-database-in-memory.md) , aby zwiększyć wydajność analizy lub przetwarzania struktur danych NoSQL, użyj [replikacji transakcyjnej](sql-database-managed-instance-transactional-replication.md) lub [możliwych do odczytu replik](sql-database-read-scale-out.md) , aby utworzyć kopię danych w innym miejscu i odciążyć niektóre obciążenia analityczne z podstawowej bazy danych.

## <a name="overview"></a>Omówienie

Usługa Azure SQL udostępnia następujące funkcje wielomodelowe:
- [Funkcje wykresu](#graph-features) umożliwiają prezentowanie danych jako zestawu węzłów i krawędzi oraz używanie standardowych zapytań Transact-SQL ulepszonych z operatorem Graph `MATCH` w celu wykonywania zapytań dotyczących danych grafu.
- [Funkcje JSON](#json-features) umożliwiają umieszczanie dokumentów JSON w tabelach, Przekształcanie danych relacyjnych z dokumentami JSON i na odwrót. Aby zoptymalizować zapytania, można użyć standardowego języka Transact-SQL rozszerzonego z funkcjami JSON w celu analizowania dokumentów i używania indeksów nieklastrowanych, indeksów magazynu kolumn lub tabel zoptymalizowanych pod kątem pamięci.
- [Funkcje przestrzenne](#spatial-features) umożliwiają przechowywanie danych geograficznych i geometrycznych, indeksowanie ich przy użyciu indeksów przestrzennych oraz pobieranie danych przy użyciu zapytań przestrzennych.
- [Funkcje XML](#xml-features) umożliwiają przechowywanie i indeksowanie danych XML w bazie danych i używanie natywnych operacji XQuery/XPath do pracy z danymi XML. Usługa Azure SQL Database ma wyspecjalizowany wbudowany aparat zapytań XML, który przetwarza dane XML.
- [Pary klucz-wartość](#key-value-pairs) nie są jawnie obsługiwane jako funkcje specjalne, ponieważ pary klucz-wartość mogą być natywnie modelowane jako tabele dwukolumnowe.

  > [!Note]
  > Można użyć wyrażenia ścieżki JSON, wyrażeń XQuery/XPath, funkcji przestrzennych i wyrażeń kwerend grafu w tym samym zapytaniu języka Transact-SQL, aby uzyskać dostęp do wszystkich danych przechowywanych w bazie danych. Ponadto wszystkie narzędzia i języki programowania, które mogą wykonywać zapytania Transact-SQL, mogą również używać tego interfejsu zapytania do uzyskiwania dostępu do danych z wielu modeli. Jest to kluczowa różnica w porównaniu z wielomodelowymi bazami danych, takimi jak [Azure Cosmos DB](/azure/cosmos-db/) , które udostępnia WYSPECJALIZOWANY interfejs API dla różnych modeli danych.

W poniższych sekcjach znajdują się informacje dotyczące najważniejszych możliwości dla wielu modeli platformy Azure SQL Database.

## <a name="graph-features"></a>Funkcjonalności grafu

Azure SQL Database oferuje możliwości bazy danych programu Graph do modelowania relacji wiele-do-wielu w bazie danych. Wykres jest kolekcją węzłów (lub wierzchołków) oraz krawędzi (lub relacji). Węzeł reprezentuje jednostkę (na przykład osobę lub organizację), a krawędź reprezentuje relację między dwoma węzłami, które nawiązuje połączenie (na przykład polubień lub znajomych). Poniżej przedstawiono niektóre funkcje, które sprawiają, że baza danych grafu jest unikatowa:
- Krawędzie lub relacje są pierwszymi jednostkami klasy w bazie danych grafu i mogą mieć skojarzone z nimi atrybuty lub właściwości.
- Pojedyncze krawędzie mogą elastycznie łączyć wiele węzłów w bazie danych grafu.
- W łatwy sposób można wyznaczać zapytania nawigacji dotyczące dopasowywania wzorców i wieloskoków.
- Można łatwo wyrazić przechodnie zapytania o zamknięciu i polimorficznym.

Relacje grafu i możliwości zapytania grafu są zintegrowane z językiem Transact-SQL i korzystają z zalet SQL Server jako systemu zarządzania bazami danych.
[Przetwarzanie grafu](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) to podstawowa funkcja aparatu bazy danych SQL Server, dzięki czemu można znaleźć więcej informacji na temat przetwarzania grafu.

### <a name="when-to-use-a-graph-capability"></a>Kiedy używać funkcji grafu

Brak bazy danych grafu, której nie można osiągnąć, przy użyciu relacyjnej bazy danych. Jednak baza danych grafu może ułatwić wyrażanie niektórych zapytań. Decyzja o wyborze jednego z nich może opierać się na następujących czynnikach:

- Dane hierarchiczne modelu, w których jeden węzeł może mieć wiele elementów nadrzędnych, więc nie można użyć HierarchyId
- Model ma swoją aplikację złożoną relację wiele-do-wielu; w miarę rozwoju aplikacji dodawane są nowe relacje.
- Należy analizować połączone dane i relacje.

## <a name="json-features"></a>Funkcje JSON

Azure SQL Database umożliwia analizowanie i wykonywanie zapytań o dane reprezentowane w formacie JavaScript Object Notation [(JSON)](https://www.json.org/) i eksportowanie danych relacyjnych jako tekstu JSON.

JSON to popularny format danych używany do wymiany danych w nowoczesnych aplikacjach sieci Web i mobilnych. KOD JSON jest również używany do przechowywania danych z częściową strukturą w plikach dziennika lub w bazach danych NoSQL, takich jak [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Wiele usług sieci Web REST zwraca wyniki sformatowane jako tekst JSON lub Akceptuję dane sformatowane w formacie JSON. Większość usług platformy Azure, takich jak [azure wyszukiwanie poznawcze](https://azure.microsoft.com/services/search/), [azure Storage](https://azure.microsoft.com/services/storage/)i [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) , mają punkty końcowe REST, które zwracają lub zużywają kod JSON.

Azure SQL Database umożliwia łatwe korzystanie z danych JSON i integrację bazy danych z nowoczesnymi usługami. Azure SQL Database udostępnia następujące funkcje do pracy z danymi JSON:

![Funkcje JSON](./media/sql-database-json-features/image_1.png)

W przypadku tekstu JSON można wyodrębnić dane z pliku JSON lub sprawdzić, czy format JSON jest prawidłowo sformatowany przy użyciu wbudowanych funkcji [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)i [isjson](https://msdn.microsoft.com/library/dn921896.aspx). Funkcja [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) umożliwia zaktualizowanie wartości wewnątrz tekstu JSON. Aby uzyskać bardziej zaawansowane zapytania i analizę, funkcja [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) może przekształcić tablicę obiektów JSON w zestaw wierszy. Wszystkie zapytania SQL można wykonać na zwracanym zestawie wyników. Na koniec istnieje klauzula [for JSON](https://msdn.microsoft.com/library/dn921882.aspx) , która umożliwia formatowanie danych przechowywanych w tabelach relacyjnych jako tekstu JSON.

Aby uzyskać więcej informacji, zobacz [jak korzystać z danych JSON w usłudze azure SQL Database](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) to podstawowa funkcja aparatu bazy danych SQL Server, dzięki czemu można znaleźć więcej informacji na temat funkcji JSON.

### <a name="when-to-use-a-json-capability"></a>Kiedy używać funkcji JSON

Modele dokumentów mogą być używane zamiast modeli relacyjnych w niektórych scenariuszach:
- Wysoka normalizacja schematu nie przynosi znaczących korzyści, ponieważ uzyskuje się dostęp do wszystkich pól jednocześnie, lub nigdy nie aktualizuje się znormalizowanych części obiektów. Jednak znormalizowany model zwiększa złożoność zapytań ze względu na dużą liczbę tabel, które należy dołączyć, aby uzyskać dane.
- Pracujesz z aplikacjami, które natywnie wykorzystują Dokumenty JSON, są komunikacyjne lub modele danych i nie chcesz wprowadzać dodatkowych warstw, które przekształcają dane relacyjne na format JSON i odwrotnie.
- Należy uprościć model danych przez cofnięcie normalizacji tabel podrzędnych lub wzorców wartości obiektów jednostek.
- Należy załadować lub wyeksportować dane przechowywane w formacie JSON bez dodatkowego narzędzia, które analizuje dane.

## <a name="spatial-features"></a>Funkcje przestrzenne

Dane przestrzenne reprezentują informacje o lokalizacji fizycznej i kształcie obiektów geometrycznych. Te obiekty mogą wskazywać lokalizacje lub bardziej złożone obiekty, takie jak kraje/regiony, drogi lub jeziora.

Azure SQL Database obsługuje dwa typy danych przestrzennych — typ danych geometrii i typ danych Geografia.
- Typ geometrii reprezentuje dane w układzie współrzędnych Euclidean (płaskim).
- Typ geografii reprezentuje dane w układzie współrzędnych rundy-ziemi.

Istnieje wiele obiektów przestrzennych, których można używać w usłudze Azure SQL Database, takich jak [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Wielokąt](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)itp.

Azure SQL Database również udostępnia wyspecjalizowane [indeksy przestrzenne](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) , których można użyć w celu poprawienia wydajności zapytań przestrzennych.

[Obsługa przestrzenna](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) to podstawowa funkcja aparatu bazy danych SQL Server, dzięki czemu można znaleźć więcej informacji na temat funkcji przestrzennej.

## <a name="xml-features"></a>Funkcje XML

SQL Server zapewnia zaawansowaną platformę do tworzenia rozbudowanych aplikacji do zarządzania danymi z częściową strukturą. Obsługa języka XML jest zintegrowana ze wszystkimi składnikami w SQL Server i obejmuje następujące elementy:

- Typ danych XML. Wartości XML mogą być przechowywane natywnie w kolumnie typu danych XML, które można wpisywać przy użyciu kolekcji schematów XML lub z lewej strony. Można indeksować kolumnę XML.
- Możliwość określenia zapytania XQuery dla danych XML przechowywanych w kolumnach i zmiennych typu XML. Funkcji XQuery można używać w dowolnym zapytaniu języka Transact-SQL, który uzyskuje dostęp do dowolnego modelu danych używanego w bazie danych.
- Automatycznie Indeksuj wszystkie elementy w dokumentach XML przy użyciu [podstawowego indeksu XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) lub określ dokładne ścieżki, które powinny być indeksowane przy użyciu [pomocniczego indeksu XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET, która umożliwia ładowanie zbiorcze danych XML.
- Przekształć dane relacyjne w formacie XML.

[Język XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) jest podstawową funkcją aparatu bazy danych SQL Server, dzięki czemu można znaleźć więcej informacji na temat funkcji XML.

### <a name="when-to-use-an-xml-capability"></a>Kiedy używać funkcji XML

Modele dokumentów mogą być używane zamiast modeli relacyjnych w niektórych scenariuszach:
- Wysoka normalizacja schematu nie przynosi znaczących korzyści, ponieważ uzyskuje się dostęp do wszystkich pól jednocześnie, lub nigdy nie aktualizuje się znormalizowanych części obiektów. Jednak znormalizowany model zwiększa złożoność zapytań ze względu na dużą liczbę tabel, które należy dołączyć, aby uzyskać dane.
- Pracujesz z aplikacjami, które natywnie wykorzystują dokumenty XML, są komunikacyjne lub modele danych i nie chcesz wprowadzać dodatkowych warstw, które przekształcają dane relacyjne na XML i odwrotnie.
- Należy uprościć model danych przez cofnięcie normalizacji tabel podrzędnych lub wzorców wartości obiektów jednostek.
- Należy załadować lub wyeksportować dane przechowywane w formacie XML bez dodatkowego narzędzia, które analizuje dane.

## <a name="key-value-pairs"></a>Pary klucz-wartość

Azure SQL Database nie mają wyspecjalizowanych typów ani struktur, które obsługują pary klucz-wartość, ponieważ struktury klucz-wartość mogą być natywnie reprezentowane jako standardowe tabele relacyjne:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Tę strukturę klucz-wartość można dostosować do własnych potrzeb bez ograniczeń. Przykładowo wartością może być dokument XML, a nie `nvarchar(max)` typ, jeśli wartość jest dokumentu JSON, można umieścić `CHECK` ograniczenie, które weryfikuje ważność zawartości JSON. Można umieścić dowolną liczbę wartości związanych z jednym kluczem w dodatkowych kolumnach, dodać kolumny obliczane i indeksy, aby uprościć i zoptymalizować dostęp do danych, zdefiniować tabelę jako pamięć/zoptymalizowaną pod względem schematu tabelę, aby uzyskać lepszą wydajność itd.

Zobacz, w [jaki sposób BWin korzysta z OLTP w pamięci, aby osiągnąć niespotykaną wydajność i skalę](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) dla swojego rozwiązania pamięci podręcznej ASP.NET, które osiągnęło 1.200.000 partie w ciągu kilku sekund, jako przykład sposobu, w jaki model relacyjny może być efektywnie używany jako rozwiązanie par klucz-wartość w praktyce.

## <a name="next-steps"></a>Następne kroki
Funkcje wielomodelowe w bazach danych Azure SQL są również podstawowymi funkcjami aparatu bazy danych SQL Server, które są współużytkowane przez Azure SQL Database i SQL Server. Aby dowiedzieć się więcej o tych funkcjach, odwiedź stronę dokumentacji relacyjnej bazy danych SQL:

* [Przetwarzanie grafu](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Dane JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Obsługa przestrzenna](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [dane XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
