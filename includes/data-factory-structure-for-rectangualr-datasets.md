---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183901"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Określanie definicji struktury dla prostokątnych zestawów danych
Sekcja struktury w formacie JSON zestawów danych jest **opcjonalną** sekcją dla prostokątnych tabel (z wierszami & kolumnami) i zawiera kolekcję kolumn dla tabeli. Sekcja struktury zostanie użyta do udostępnienia informacji o typie dla konwersji typów lub wykonywania mapowań kolumn. Poniższe sekcje zawierają szczegółowe informacje o tych funkcjach. 

Każda kolumna zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa kolumny. |Tak |
| typ |Typ danych kolumny. Zobacz sekcję konwersje typów poniżej, aby uzyskać więcej informacji na temat sytuacji, w których należy określić informacje o typie |Nie |
| kultura |Kultura oparta na platformie .NET, która ma być używana, jeśli określono typ i jest typem DateTime lub DateTimeOffset. Wartość domyślna to "pl-US". |Nie |
| format |Ciąg formatu, który ma być używany, gdy typ jest określony, a jest typem DateTime lub DateTimeOffset. |Nie |

Poniższy przykład pokazuje kod JSON sekcji struktury dla tabeli, która ma trzy kolumny UserID, Name i LastLoginDate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Skorzystaj z poniższych wskazówek dotyczących sytuacji, w których należy uwzględnić informacje o strukturze i co należy uwzględnić w sekcji **Struktura** .

* **W przypadku tabelarycznych źródeł danych** , które przechowują informacje o schemacie i typie danych wraz z samymi danymi (źródłami, takimi jak SQL Server, Oracle, Azure Table itp.), należy określić sekcję "struktura" tylko wtedy, gdy chcesz, aby Mapowanie kolumn określonych kolumn źródłowych do określonych kolumn w usłudze sink i ich nazwy nie były takie same (zobacz szczegóły w sekcji Mapowanie kolumn poniżej) 
  
    Jak wspomniano powyżej, informacje o typie są opcjonalne w sekcji "struktura". W przypadku źródeł strukturalnych informacje o typie są już dostępne jako część definicji zestawu danych w magazynie danych, więc nie należy uwzględniać informacji o typie, gdy zostanie uwzględniona sekcja "struktura".
* W **przypadku schematu na potrzeby odczytywania źródeł danych (w przypadku obiektów blob platformy Azure)** można wybrać przechowywanie danych bez zapisywania jakichkolwiek schematów lub informacji o typie danych. W przypadku tych typów źródeł danych należy uwzględnić "strukturę" w następujących dwóch przypadkach:
  * Chcesz utworzyć mapowanie kolumn.
  * Gdy zestaw danych jest źródłem w działaniu kopiowania, można podać informacje o typie w "strukturze", a Fabryka danych będzie używać tego typu informacji do konwersji na typy natywne dla ujścia. Więcej informacji można znaleźć w artykule [przenoszenie danych do i z usługi Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) .

### <a name="supported-net-based-types"></a>Obsługiwał. Typy oparte na sieci
Usługa Data Factory obsługuje następujące wartości typu opartego na technologii .NET zgodne ze specyfikacją CLS na potrzeby udostępniania informacji o typie w "strukturze" dla schematu na potrzeby odczytywania źródeł danych, takich jak Azure Blob.

* Int16
* Int32 
* Int64
* Pojedynczy
* Double
* Liczba dziesiętna
* Byte []
* Wartość logiczna
* Ciąg 
* Guid (identyfikator GUID)
* Datetime (data/godzina)
* DateTimeOffset
* Zakres czasu 

Dla elementu DateTime & DateTimeOffset można także opcjonalnie określić ciąg "Culture" & "format", aby ułatwić analizowanie niestandardowego ciągu DateTime. Zobacz przykład dla konwersji typów poniżej.

