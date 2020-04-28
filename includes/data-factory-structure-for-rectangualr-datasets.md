---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183901"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Określanie definicji struktury dla prostokątnych zestawów danych
Sekcja struktury w zestawach danych JSON jest **opcjonalną** sekcją dla tabel prostokątnych (z wierszami & kolumnami) i zawiera zbiór kolumn dla tabeli. Użyjesz sekcji struktury do dostarczania informacji o typie dla konwersji typów lub mapowania kolumn. W poniższych sekcjach opisano te funkcje szczegółowo. 

Każda kolumna zawiera następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| name |Nazwa kolumny. |Yes |
| type |Typ danych kolumny. Zobacz sekcję konwersji typów poniżej, aby uzyskać więcej informacji na temat tego, kiedy należy określić informacje o typie |Nie |
| kultura |.NET na podstawie kultury, która ma być używana, gdy typ jest określony i jest typu .NET Datetime lub Datetimeoffset. Wartość domyślna to "en-us". |Nie |
| format |Ciąg formatu, który ma być używany, gdy typ jest określony i jest typu .NET Datetime lub Datetimeoffset. |Nie |

W poniższym przykładzie przedstawiono sekcję struktury JSON dla tabeli, która ma trzy kolumny userid, name i lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Skorzystaj z poniższych wytycznych dotyczących tego, kiedy należy uwzględnić informacje o "strukturze" i co należy uwzględnić w sekcji **struktury.**

* **Dla ustrukturyzowanych źródeł danych,** które przechowują schemat danych i wpisywać informacje wraz z samymi danymi (źródła takie jak SQL Server, Oracle, tabela platformy Azure itp.), należy określić sekcję "struktura" tylko wtedy, gdy chcesz zrobić mapowanie kolumn określonych kolumn źródłowych do określonych kolumn w zmieniu, a ich nazwy nie są takie same (zobacz szczegóły w sekcji mapowania kolumn poniżej). 
  
    Jak wspomniano powyżej, informacje o typie są opcjonalne w sekcji "struktura". W przypadku źródeł strukturalnych informacje o typie są już dostępne jako część definicji zestawu danych w magazynie danych, więc nie należy dołączać informacji o typie, jeśli zostanie uwzględniona sekcja "struktura".
* **Dla schematu w źródłach odczytu danych (w szczególności azure blob)** można wybrać do przechowywania danych bez przechowywania żadnych schematów lub wpisywać informacje z danymi. Dla tych typów źródeł danych należy uwzględnić "strukturę" w następujących 2 przypadkach:
  * Chcesz zrobić mapowanie kolumn.
  * Gdy zestaw danych jest źródłem w copy działania, można podać informacje o typie w "struktura" i fabryka danych użyje tego typu informacji do konwersji do typów macierzystych dla ujścia. Aby uzyskać więcej [informacji, zobacz Przenoszenie danych do i z usługi Azure Blob.](../articles/data-factory/v1/data-factory-azure-blob-connector.md)

### <a name="supported-net-based-types"></a>Obsługiwane. Typy oparte na sieci
Fabryka danych obsługuje następujące wartości typu .NET zgodne ze specyfikacją CLS w celu zapewnienia informacji o typie w "strukturze" dla schematu w źródłach odczytu danych, takich jak obiekt blob platformy Azure.

* Int16
* Int32 
* Int64
* Single
* Double
* Wartość dziesiętna
* Bajt[]
* Wartość logiczna
* Ciąg 
* Guid (identyfikator GUID)
* Datetime (data/godzina)
* Datetimeoffset
* Zakres czasu 

Dla Datetime & Datetimeoffset można również opcjonalnie określić ciąg "culture" & "format", aby ułatwić analizowanie niestandardowego ciągu Datetime. Poniżej znajduje się przykładowa konwersja typu.

