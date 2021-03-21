---
title: Modelowanie danych relacyjnych SQL na potrzeby importowania i indeksowania
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak modelować dane relacyjne, nieznormalizowane do płaskiego zestawu wyników, na potrzeby indeksowania i wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88924524"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Jak modelować relacyjne dane SQL na potrzeby importowania i indeksowania na platformie Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze akceptuje płasky zestaw wierszy jako dane wejściowe do [potoku indeksowania](search-what-is-an-index.md). Jeśli dane źródłowe pochodzą z sprzężonych tabel w SQL Server relacyjnej bazie danych, w tym artykule opisano sposób konstruowania zestawu wyników i sposób modelowania relacji nadrzędny-podrzędny w indeksie Wyszukiwanie poznawcze platformy Azure.

Jako ilustracja odwołujemy się do hipotetycznej bazy danych hoteli na podstawie [danych demonstracyjnych](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels). Załóżmy, że baza danych zawiera tabelę hoteli $ z 50 hoteli, oraz tabelę pokojów $ z pomieszczeń różnych typów, stawek i walorów, co obejmuje łącznie 750 pokojów. Istnieje relacja jeden do wielu między tabelami. W naszym podejściu widok dostarczy zapytania, które zwraca 50 wierszy, jeden wiersz na Hotel, wraz z powiązanymi szczegółami pokoju osadzonymi w każdym wierszu.

   ![Tabele i widok w bazie danych hoteli](media/index-sql-relational-data/hotels-database-tables-view.png "Tabele i widok w bazie danych hoteli")


## <a name="the-problem-of-denormalized-data"></a>Problem z nieznormalizowanymi danymi

Jednym z wyzwań związanych z pracą z relacjami "jeden do wielu" jest to, że standardowe zapytania utworzone w sprzężonych tabelach zwróciją nieznormalizowane dane, co nie działa prawidłowo w scenariuszu Wyszukiwanie poznawcze platformy Azure. Rozważmy poniższy przykład, który sprzęga Hotele i pokoje.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Wyniki tego zapytania zwracają wszystkie pola hotelowe, a następnie wszystkie pola Room z informacjami o wstępnym hotelu dla każdej wartości pokojowej.

   ![Nieznormalizowane dane, nadmiarowe dane hotelu w przypadku dodania pól pokoju](media/index-sql-relational-data/denormalize-data-query.png "Nieznormalizowane dane, nadmiarowe dane hotelu w przypadku dodania pól pokoju")


Gdy to zapytanie powiodło się na powierzchni (dostarczając wszystkie dane w płaskim zestawie wierszy), kończy się niepowodzeniem w dostarczaniu właściwej struktury dokumentu dla oczekiwanego środowiska wyszukiwania. Podczas indeksowania usługa Azure Wyszukiwanie poznawcze utworzy jeden dokument wyszukiwania dla każdego wiersza. Jeśli dokumenty wyszukiwania wyglądały jak powyższe wyniki, można było postrzegać duplikaty siedmiu oddzielnych dokumentów dla wieloosiowego hotelu. Zapytanie dotyczące "hoteli w Florida" zwróci siedem wyników w przypadku hotelu, który jest przeznaczony tylko dla bliźniaczych hoteli, wypychanie innych ważnych hoteli do wyników wyszukiwania.

Aby uzyskać oczekiwane doświadczenie w odniesieniu do jednego dokumentu na Hotel, należy dostarczyć zestaw wierszy z prawej strony, ale z pełnymi informacjami. Na szczęście można to łatwo zrobić, wdrażając techniki w tym artykule.

## <a name="define-a-query-that-returns-embedded-json"></a>Zdefiniuj zapytanie zwracające osadzony kod JSON

Aby zapewnić oczekiwany komfort wyszukiwania, zestaw danych powinien zawierać jeden wiersz dla każdego dokumentu wyszukiwania w usłudze Azure Wyszukiwanie poznawcze. W naszym przykładzie chcemy mieć jeden wiersz dla każdego hotelu, ale chcemy również, aby nasi użytkownicy mogli wyszukiwać inne powiązane z nimi pola, takie jak nocna stawka, rozmiar i liczba Beds, lub widok sekwencje, z których wszystkie są częścią szczegółowych informacji o pokoju.

Rozwiązaniem jest przechwycenie szczegółów pokoju jako zagnieżdżonego pliku JSON, a następnie wstawienie struktury JSON do pola w widoku, jak pokazano w drugim kroku. 

1. Załóżmy, że masz dwie sprzężone tabele, Hotele $ i pokoje $, które zawierają szczegóły dotyczące 50 hoteli i 750 pokojów i są przyłączone do pola HotelID. Osobno te tabele zawierają 50 hoteli i 750 pokrewne pokoje.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Utwórz widok składający się ze wszystkich pól w tabeli nadrzędnej ( `SELECT * from dbo.Hotels$` ) z dodaniem nowego pola *pokojów* , które zawiera dane wyjściowe zapytania zagnieżdżonego. Klauzula **for JSON dla Autotekstu** w `SELECT * from dbo.Rooms$` strukturach danych wyjściowych w formacie JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   Poniższy zrzut ekranu przedstawia widok wyników, z polem nvarchar *pomieszczeń* u dołu. Pole *pokojów* istnieje tylko w widoku HotelRooms.

   ![Widok HotelRooms](media/index-sql-relational-data/hotelsrooms-view.png "Widok HoteRooms")

1. Uruchom, `SELECT * FROM dbo.HotelRooms` Aby pobrać zestaw wierszy. To zapytanie zwraca 50 wierszy, jeden na Hotel, ze skojarzonymi informacjami o pokoju jako kolekcją JSON. 

   ![Zestaw wierszy z widoku HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Zestaw wierszy z widoku HotelRooms")

Ten zestaw wierszy jest teraz gotowy do zaimportowania do Wyszukiwanie poznawcze platformy Azure.

> [!NOTE]
> W tym podejściu przyjęto założenie, że osadzony kod JSON [przekracza maksymalne limity rozmiaru kolumn SQL Server](/sql/sql-server/maximum-capacity-specifications-for-sql-server). 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Użyj złożonej kolekcji dla strony "wiele" relacji jeden-do-wielu

Na stronie Wyszukiwanie poznawcze platformy Azure Utwórz schemat indeksu, który modeluje relację "jeden do wielu" przy użyciu zagnieżdżonego kodu JSON. Zestaw wyników utworzony w poprzedniej sekcji zazwyczaj odpowiada schematowi indeksu podanemu poniżej (Obcinamy niektóre pola dla zwięzłości).

Poniższy przykład jest podobny do przykładu w temacie [jak modelować złożone typy danych](search-howto-complex-data-types.md#creating-complex-fields). Struktura *pokojów* , która koncentruje się na tym artykule, znajduje się w kolekcji Fields o nazwie *Hotele*. Ten przykład pokazuje również typ złożony dla *adresu*, który różni się od *pokojów* , które składa się ze stałego zestawu elementów, w przeciwieństwie do wielokrotności, dowolnej liczby elementów dozwolonych w kolekcji.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Uwzględniając poprzedni zestaw wyników i powyższy schemat indeksu, masz wszystkie wymagane składniki dla pomyślnej operacji indeksowania. Spłaszczony zestaw danych spełnia wymagania dotyczące indeksowania, ale zachowuje szczegółowe informacje. W indeksie Wyszukiwanie poznawcze platformy Azure wyniki wyszukiwania będą łatwo znajdować się w jednostkach opartych na hotelu, zachowując kontekst poszczególnych pokojów i ich atrybuty.

## <a name="next-steps"></a>Następne kroki

Za pomocą własnego zestawu danych można utworzyć i załadować indeks za pomocą [Kreatora importu danych](search-import-data-portal.md) . Kreator wykrywa osadzoną kolekcję JSON, taką jak element zawarty w *pokojach*, i wnioskuje schemat indeksu, który zawiera kolekcję typu złożonego. 

  ![Kreator wywnioskowany przez Kreatora importu danych](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Kreator wywnioskowany przez Kreatora importu danych")

Skorzystaj z poniższego przewodnika Szybki Start, aby poznać podstawowe kroki Kreatora importu danych.

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie indeksu wyszukiwania przy użyciu Azure Portal](search-get-started-portal.md)