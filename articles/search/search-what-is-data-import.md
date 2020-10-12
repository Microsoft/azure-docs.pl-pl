---
title: Importowanie i pozyskiwanie danych w indeksach wyszukiwania
titleSuffix: Azure Cognitive Search
description: Wypełnianie i przekazywanie danych do indeksu na platformie Azure Wyszukiwanie poznawcze z zewnętrznych źródeł danych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 148310419ad4f760219003514dbc078b7c675be6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538791"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Omówienie importowania danych — Wyszukiwanie poznawcze platformy Azure

Na platformie Azure Wyszukiwanie poznawcze zapytania wykonują zawartość załadowana do i zapisane w [indeksie wyszukiwania](search-what-is-an-index.md). W tym artykule opisano dwa podstawowe podejścia do wypełniania indeksu: *wypychanie* danych do indeksu programowo lub wskazywanie [usługi Azure wyszukiwanie poznawcze Indexer](search-indexer-overview.md) w obsługiwanym źródle danych w celu *ściągnięcia* danych.

W obu przypadkach celem jest załadowanie danych z zewnętrznego źródła danych do indeksu Wyszukiwanie poznawcze platformy Azure. Dzięki usłudze Azure Wyszukiwanie poznawcze można utworzyć pusty indeks, ale do momentu wypchnięcia lub ściągnięcia danych nie będzie queryable.

> [!NOTE]
> Jeśli [wzbogacanie AI](cognitive-search-concept-intro.md) jest wymaganiem rozwiązania, należy użyć modelu ściągania (indeksatorów) do załadowania indeksu. Przetwarzanie zewnętrzne jest obsługiwane tylko przez umiejętności dołączone do indeksatora.

## <a name="pushing-data-to-an-index"></a>Wypychanie danych do indeksu

Model wypychania używany do programistycznego wysyłania danych do usługi Azure Wyszukiwanie poznawcze jest najbardziej elastycznym podejściem. Po pierwsze nie ma żadnych ograniczeń dotyczących typu źródła danych. Każdy zestaw danych składający się z dokumentów JSON można wypchnąć do indeksu Wyszukiwanie poznawcze platformy Azure, zakładając, że każdy dokument w zestawie danych ma pola mapowania do pól zdefiniowanych w schemacie indeksu. Po drugie nie ma żadnych ograniczeń dotyczących częstotliwości wykonywania. Możesz wypychać zmiany do indeksu tak często, jak tylko chcesz. W przypadku aplikacji wymagających bardzo niskich opóźnień (na przykład w razie konieczności synchronizowania operacji wyszukiwania z dynamicznymi bazami danych zapasów) model wypychania jest jedynym rozwiązaniem.

To podejście jest bardziej elastyczne niż model polegający na ściąganiu, ponieważ możesz przekazywać dokumenty pojedynczo lub w partiach (maksymalnie 1000 dokumentów na partię lub 16 MB zależnie od tego, który limit zostanie osiągnięty jako pierwszy). Model wypychania umożliwia również przekazywanie dokumentów do usługi Azure Wyszukiwanie poznawcze niezależnie od tego, gdzie są dane.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Jak wypchnąć dane do indeksu usługi Azure Wyszukiwanie poznawcze

Możesz załadować jeden lub wiele dokumentów do indeksu przy użyciu następujących interfejsów API:

+ [Dodawanie, aktualizowanie lub usuwanie dokumentów (interfejs API REST)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Klasa indexAction](/dotnet/api/microsoft.azure.search.models.indexaction) lub [klasa indexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) 

Obecnie nie istnieje wsparcie narzędziowe wypychania danych za pośrednictwem portalu.

Aby zapoznać się z wprowadzeniem do każdej metodologii, zobacz [Szybki Start: Tworzenie indeksu wyszukiwanie poznawcze platformy Azure przy użyciu programu PowerShell](./search-get-started-powershell.md) lub [języka C# — szybki start: Tworzenie indeksu wyszukiwanie poznawcze platformy Azure przy użyciu zestawu .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Akcje indeksowania: przekazywanie, scalanie, mergeOrUpload, usuwanie

Można kontrolować typ akcji indeksowania dla poszczególnych dokumentów, określać, czy dokument powinien być przekazywany w całości, scalony z istniejącą zawartością dokumentu lub usunięty.

W interfejsie API REST wydaj żądania HTTP POST z treściami żądania JSON do adresu URL punktu końcowego indeksu usługi Azure Wyszukiwanie poznawcze. Każdy obiekt JSON w tablicy "value" zawiera klucz dokumentu i określa, czy akcja indeksowania dodaje, aktualizuje lub usuwa zawartość dokumentu. Aby zapoznać się z przykładem kodu, zobacz [ładowanie dokumentów](search-get-started-dotnet.md#load-documents).

W zestawie SDK platformy .NET Pakuj dane do `IndexBatch` obiektu. `IndexBatch`Hermetyzuje kolekcję `IndexAction` obiektów, z których każdy zawiera dokument i właściwość, która informuje platformę Azure wyszukiwanie poznawcze o działaniach wykonywanych w tym dokumencie. Aby uzyskać przykład kodu, zobacz [Przewodnik Szybki Start dotyczący języka C#](search-get-started-dotnet.md).


| @search.action | Opis | Wymagane pola dla każdego dokumentu | Uwagi |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Akcja `upload` jest podobna do akcji „upsert”, co oznacza, że dokument zostanie wstawiony, jeśli jest nowy, albo zaktualizowany/zastąpiony, jeśli już istnieje. |pole klucza oraz inne pola, które chcesz zdefiniować |Podczas aktualizowania/zastępowania istniejącego dokumentu każde pole, które nie jest określone w żądaniu, zostanie ustawione na wartość `null`. Dzieje się tak nawet wtedy, gdy pole było wcześniej ustawione na wartość inną niż null. |
| `merge` |Aktualizuje istniejący dokument o określone pola. Jeśli dokument nie istnieje w indeksie, scalanie zakończy się niepowodzeniem. |pole klucza oraz inne pola, które chcesz zdefiniować |Wszystkie pola, które określisz w żądaniu scalania, zastąpią istniejące pola w dokumencie. W zestawie SDK platformy .NET obejmuje pola typu `DataType.Collection(DataType.String)` . W interfejsie API REST obejmuje to pola typu `Collection(Edm.String)` . Jeśli na przykład dokument zawiera pole `tags` o wartości `["budget"]` i wykonywane jest scalanie z wartością `["economy", "pool"]` dla pola `tags`, końcowa wartość pola `tags` będzie równa `["economy", "pool"]`. Nie będzie to `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Ta akcja działa jak akcja `merge`, jeśli dokument o danym kluczu już istnieje w indeksie. Jeśli dokument nie istnieje, działa jak akcja `upload` dla nowego dokumentu. |pole klucza oraz inne pola, które chcesz zdefiniować |- |
| `delete` |Usuwa określony dokument z indeksu. |tylko pole klucza |Wszystkie pola, które określisz oprócz pola klucza, zostaną zignorowane. Jeśli chcesz usunąć pojedyncze pole z dokumentu, zamiast tej akcji użyj akcji `merge` i po prostu jawnie ustaw dla pola wartość null. |

### <a name="formulate-your-query"></a>Formułowanie zapytania

Istnieją dwie metody [przeszukiwania indeksu przy użyciu interfejsu API REST](/rest/api/searchservice/Search-Documents). Pierwsza z nich polega na wysłaniu żądania HTTP POST, w ramach którego parametry zapytania są definiowane w obiekcie JSON w treści żądania. Druga metoda obejmuje wysłanie żądania HTTP GET, w ramach którego parametry zapytania są definiowane w adresie URL żądania. W przypadku żądania POST limity dotyczące rozmiaru parametrów zapytania są [luźniejsze](/rest/api/searchservice/Search-Documents) niż dla żądania GET. Z tego powodu zaleca się używanie żądania POST, o ile nie występują specjalne okoliczności, w których korzystanie z żądania GET jest wygodniejsze.

W przypadku funkcji POST i GET należy podać *nazwę usługi*, *nazwę indeksu*i *wersję interfejsu API* w adresie URL żądania. 

W przypadku żądania GET parametry zapytania są określane w *ciągu zapytania* na końcu adresu URL. Format adresu URL został przedstawiony poniżej:

```http
    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06
```

Format wpisu POST jest taki sam, ale z `api-version` parametrami ciągu zapytania.

## <a name="pulling-data-into-an-index"></a>Ściąganie danych do indeksu

Model polegający na ściąganiu obejmuje przeszukiwanie obsługiwanego źródła danych i automatyczne przekazywanie danych do indeksu. Na platformie Azure Wyszukiwanie poznawcze ta funkcja jest wdrażana za pomocą *indeksatorów*, obecnie dostępnych dla następujących platform:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database, wystąpienie zarządzane SQL i SQL Server na maszynach wirtualnych platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indeksatory łączą indeks ze źródłem danych (zwykle tabelą, widokiem lub równoważną strukturą) i mapują pola źródłowe na równoważne pola w indeksie. W czasie wykonywania zestaw wierszy jest automatycznie przekształcany w dane w formacie JSON i ładowany do określonego indeksu. Wszystkie indeksatory obsługują planowanie, dzięki czemu możesz określić, jak często dane mają być odświeżane. Większość indeksatorów udostępnia śledzenie zmian, jeśli źródło danych obsługuje tę funkcję. Dzięki śledzeniu zmian i usuwania istniejących dokumentów oraz rozpoznawaniu nowych dokumentów indeksatory eliminują konieczność aktywnego zarządzania danymi w indeksie. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Jak ściągać dane do indeksu Wyszukiwanie poznawcze platformy Azure

Funkcja indeksatora jest udostępniona w witrynie [Azure Portal](search-import-data-portal.md), interfejsie [API REST](/rest/api/searchservice/Indexer-operations) i zestawie [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Zaletą korzystania z portalu jest to, że usługa Azure Wyszukiwanie poznawcze może zazwyczaj wygenerować domyślny schemat indeksu, odczytując metadane źródłowego zestawu danych. Wygenerowany indeks można modyfikować, dopóki indeks jest przetwarzany, po czym dozwolone są tylko te zmiany schematu, które nie wymagają ponownego indeksowania. Jeśli żądane zmiany wpływają bezpośrednio na schemat, konieczne będzie odbudowanie indeksu. 

## <a name="verify-data-import-with-search-explorer"></a>Weryfikowanie importowania danych za pomocą Eksploratora wyszukiwania

Szybkim sposobem przeprowadzenia wstępnego sprawdzenia w przekazaniu dokumentu jest użycie **Eksploratora wyszukiwania** w portalu. Eksplorator umożliwia wykonywanie zapytań względem indeksu bez konieczności pisania kodu. Środowisko wyszukiwania jest oparte na ustawieniach domyślnych, takich jak [prosta składnia](/rest/api/searchservice/simple-query-syntax-in-azure-search) i domyślny [parametr zapytania searchmode](/rest/api/searchservice/search-documents). Wyniki są zwracane w formacie JSON, co umożliwia inspekcję całego dokumentu.

> [!TIP]
> Wiele [przykładów kodu wyszukiwanie poznawcze platformy Azure](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) obejmuje osadzone lub łatwo dostępne zestawy danych, oferując łatwy sposób na rozpoczęcie pracy. Portal udostępnia również przykładowy indeksator i źródło danych składające się z małego zestawu danych nieruchomości (o nazwie „realestate-us-sample”). Po uruchomieniu wstępnie skonfigurowanego indeksatora w przykładowym źródle danych indeks zostanie utworzony i załadowany z dokumentami, które mogą następnie być badane w Eksploratorze wyszukiwania lub przez zapisanie kodu.

## <a name="see-also"></a>Zobacz też

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Przewodnik po portalu: tworzenie i ładowanie indeksu oraz wykonywanie na nim zapytań](search-get-started-portal.md)