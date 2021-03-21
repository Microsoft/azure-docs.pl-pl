---
title: Powtórzona kopia w Azure Data Factory
description: Dowiedz się, jak uniknąć duplikowania, mimo że plasterek, który kopiuje dane, jest uruchamiany więcej niż raz.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c427cd90412121e896738ca43f4c66dd24b096dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387091"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Powtórzona kopia w Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Powtarzanie odczytu ze źródeł relacyjnych
Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek był uruchamiany ponownie w przypadku wystąpienia błędu. Gdy wycinek jest uruchamiany ponownie w dowolny sposób, należy się upewnić, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek.  
 
> [!NOTE]
> Poniższe przykłady są przeznaczone dla usługi Azure SQL, ale mają zastosowanie do każdego magazynu danych, który obsługuje prostokątne zestawy DataSet. Może być konieczne dostosowanie **typu** źródła i właściwości **zapytania** (na przykład: Query zamiast sqlReaderQuery) dla magazynu danych.   

Zazwyczaj podczas odczytywania z magazynów relacyjnych należy odczytywać tylko dane odpowiadające temu wycinkowi. W tym celu można użyć zmiennych systemowych WindowStart i WindowEnd dostępnych w Azure Data Factory. Przeczytaj o zmiennych i funkcjach w Azure Data Factory tym miejscu w artykule [Azure Data Factory-Functions i zmienne systemowe](data-factory-functions-variables.md) . Przykład: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
To zapytanie odczytuje dane, które mieszczą się w zakresie czasu trwania wycinków (WindowStart-> WindowEnd) z tabeli MyTable. Ponowne uruchomienie tego wycinka zawsze gwarantuje, że te same dane są odczytywane. 

W innych przypadkach może być konieczne odczytanie całej tabeli i określenie sqlReaderQuery w następujący sposób:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Powtarzalny zapis do obiektu sqlsink
Podczas kopiowania danych do **usługi Azure SQL/SQL Server** z innych magazynów danych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. 

Podczas kopiowania danych do bazy danych Azure SQL/SQL Server, działanie Copy domyślnie dołącza dane do tabeli sink. Załóżmy, że kopiujesz dane z pliku CSV (wartości rozdzielane przecinkami) zawierającego dwa rekordy do poniższej tabeli w bazie danych Azure SQL/SQL Server. Po uruchomieniu wycinka te dwa rekordy są kopiowane do tabeli SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Załóżmy, że znaleziono błędy w pliku źródłowym i Zaktualizowano ilość rury w dół z 2 do 4. Po ręcznym ponownym uruchomieniu wycinka danych dla tego okresu znajdziesz dwa nowe rekordy dołączone do bazy danych Azure SQL/SQL Server. W tym przykładzie przyjęto założenie, że żadna z kolumn w tabeli nie ma ograniczenia PRIMARY KEY.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Aby uniknąć tego zachowania, należy określić semantykę UPSERT przy użyciu jednego z następujących dwóch mechanizmów:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanizm 1: korzystanie z sqlWriterCleanupScript
Możesz użyć właściwości **sqlWriterCleanupScript** , aby oczyścić dane z tabeli ujścia przed wstawieniem danych po uruchomieniu wycinka. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Po uruchomieniu wycinka skrypt czyszczący jest uruchamiany w pierwszej kolejności, aby usunąć dane odnoszące się do wycinka z tabeli SQL. Działanie kopiowania wstawia dane do tabeli SQL. Jeśli wycinek zostanie ponownie uruchomiony, ilość jest aktualizowana zgodnie z potrzebami.

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

Działanie kopiowania uruchomiło skrypt oczyszczania, aby usunąć odpowiednie dane dla tego wycinka. Następnie odczytuje dane wejściowe z woluminu CSV (który zawiera tylko jeden rekord) i wstawiono je do tabeli. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanizm 2: korzystanie z sliceIdentifierColumnName
> [!IMPORTANT]
> Obecnie sliceIdentifierColumnName nie jest obsługiwana w przypadku usługi Azure Synapse Analytics. 

Drugim mechanizmem do osiągnięcia powtarzalności jest posiadanie dedykowanej kolumny (sliceIdentifierColumnName) w tabeli docelowej. Ta kolumna powinna być używana przez Azure Data Factory, aby zapewnić synchronizację źródła i miejsca docelowego. Takie podejście działa, gdy istnieje elastyczność zmiany lub definiowania docelowego schematu tabeli SQL. 

Ta kolumna jest używana przez Azure Data Factory do powtarzalności, a w Azure Data Factory procesu nie wprowadza żadnych zmian schematu do tabeli. Sposób użycia tej metody:

1. Zdefiniuj kolumnę typu **Binary (32)** w docelowej tabeli SQL. Ta kolumna nie powinna zawierać żadnych ograniczeń. Nadaj nazwę tej kolumnie jako AdfSliceIdentifier w tym przykładzie.


    Tabela źródłowa:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Tabela docelowa: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Użyj go w działaniu kopiowania w następujący sposób:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory wypełnia tę kolumnę zgodnie z potrzebami, aby upewnić się, że źródłowa i docelowa pozostała synchronizacja. Wartości tej kolumny nie należy używać poza tym kontekstem. 

Podobnie jak mechanizm 1, działanie Copy automatycznie czyści dane dla danego wycinka z docelowej tabeli SQL. Następnie wstawia dane ze źródła do tabeli docelowej. 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi łączników, które zawierają kompletne przykłady kodu JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
