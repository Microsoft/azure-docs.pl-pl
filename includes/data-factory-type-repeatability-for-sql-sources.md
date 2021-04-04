---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 9447cec55c53861ca57d5416a91ffefd35fdd20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571885"
---
## <a name="repeatability-during-copy"></a>Powtarzalność podczas kopiowania
Podczas kopiowania danych do usługi Azure SQL/SQL Server z innych magazynów danych jeden musi mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. 

Podczas kopiowania danych do usługi Azure SQL/SQL Server Database, działanie Copy domyślnie dołącza zestaw danych do tabeli sink. Na przykład podczas kopiowania danych z woluminu CSV (dane z wartościami rozdzielanymi przecinkami) zawierającego dwa rekordy do bazy danych Azure SQL/SQL Server, jest to wygląd tabeli:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Załóżmy, że znaleziono błędy w pliku źródłowym i Zaktualizowano ilość rury w dół z 2 do 4 w pliku źródłowym. Po ponownym uruchomieniu wycinka danych dla tego okresu znajdziesz dwa nowe rekordy dołączone do bazy danych Azure SQL/SQL Server. Poniżej założono, że żadna z kolumn w tabeli nie ma ograniczenia PRIMARY KEY.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Aby tego uniknąć, należy określić semantykę UPSERT przy użyciu jednego z poniższych dwóch mechanizmów wymienionych poniżej.

> [!NOTE]
> Wycinek można automatycznie uruchomić ponownie w Azure Data Factory zgodnie z określonymi zasadami ponawiania.
> 
> 

### <a name="mechanism-1"></a>Mechanizm 1
Możesz użyć właściwości **sqlWriterCleanupScript** , aby najpierw wykonać akcję oczyszczania po uruchomieniu wycinka. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Skrypt czyszczący zostanie wykonany w pierwszej kolejności podczas kopiowania dla danego wycinka, co spowoduje usunięcie danych z tabeli SQL odpowiadającej temu wycinkowi. Działanie spowoduje następnie wstawienie danych do tabeli SQL. 

Jeśli wycinek jest teraz ponownie uruchamiany, ilość będzie aktualizowana zgodnie z potrzebami.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Załóżmy, że rekord płaskich spryskiwaczy został usunięty z oryginalnego woluminu CSV. Następnie ponowne uruchomienie wycinka spowoduje wygenerowanie następującego wyniku: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nie trzeba niczego robić. Działanie kopiowania uruchomiło skrypt oczyszczania, aby usunąć odpowiednie dane dla tego wycinka. Następnie odczytuje dane wejściowe z woluminu CSV (który zawiera tylko 1 rekord) i wstawiono je do tabeli. 

### <a name="mechanism-2"></a>Mechanizm 2
> [!IMPORTANT]
> Usługa sliceIdentifierColumnName Azure Synapse Analytics nie jest w tej chwili obsługiwana. 

Innym mechanizmem do osiągnięcia powtarzalności jest posiadanie dedykowanej kolumny (**sliceIdentifierColumnName**) w tabeli docelowej. Ta kolumna powinna być używana przez Azure Data Factory, aby zapewnić synchronizację źródła i miejsca docelowego. Takie podejście działa, gdy istnieje elastyczność zmiany lub definiowania docelowego schematu tabeli SQL. 

Ta kolumna będzie używana przez Azure Data Factory do celów powtarzalności, a w Azure Data Factory procesu nie wprowadza żadnych zmian schematu w tabeli. Sposób użycia tej metody:

1. Zdefiniuj kolumnę typu binary (32) w docelowej tabeli SQL. Ta kolumna nie powinna zawierać żadnych ograniczeń. Nadaj nazwę tej kolumnie jako "ColumnForADFuseOnly" w tym przykładzie.
2. Użyj go w działaniu kopiowania w następujący sposób:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory wypełni tę kolumnę zgodnie z potrzebami, aby upewnić się, że źródło i miejsce docelowe pozostają zsynchronizowane. Wartości tej kolumny nie należy używać poza tym kontekstem przez użytkownika. 

Podobnie jak w przypadku mechanizmu 1, działanie Copy automatycznie wyczyści dane dla danego wycinka z docelowej tabeli SQL, a następnie uruchomi działanie kopiowania w normalny sposób, aby wstawić dane ze źródła do miejsca docelowego dla tego wycinka. 

