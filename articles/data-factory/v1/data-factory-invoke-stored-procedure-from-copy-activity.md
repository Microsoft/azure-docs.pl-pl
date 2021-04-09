---
title: Wywołaj procedurę składowaną z działania Azure Data Factory Copy
description: Dowiedz się, jak wywołać procedurę składowaną w Azure SQL Database lub SQL Server z działania kopiowania Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6f06b84ac0807a37c7adc603a557894be85a4cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374970"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Wywołaj procedurę składowaną z działania kopiowania w Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przekształć dane za pomocą działania procedury składowanej w Data Factory](../transform-data-using-stored-procedure.md).


Podczas kopiowania danych do [SQL Server](data-factory-sqlserver-connector.md) lub [Azure SQL Database](data-factory-azure-sql-connector.md), można skonfigurować działanie **sqlsink** w działaniu Copy, aby wywołać procedurę składowaną. Możesz chcieć użyć procedury składowanej, aby wykonać wszelkie dodatkowe przetwarzanie (Scalanie kolumn, wyszukiwanie wartości, wstawianie do wielu tabel itp.) jest wymagane przed wstawieniem danych do tabeli docelowej. Ta funkcja wykorzystuje parametry z [wartościami przechowywanymi w tabeli](/dotnet/framework/data/adonet/sql/table-valued-parameters). 

Poniższy przykład pokazuje, jak wywołać procedurę składowaną w bazie danych SQL Server z potoku Data Factory (działanie kopiowania):  

## <a name="output-dataset-json"></a>JSON zestawu danych wyjściowych
W pliku JSON wyjściowego zestawu danych ustaw **Typ** na: **SqlServerName**. Ustaw **wartość azuresqltable** do użycia z Azure SQL Database. Wartość właściwości **TableName** musi być zgodna z nazwą pierwszego parametru procedury składowanej.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>Sekcja sqlsink w formacie JSON działania kopiowania
Zdefiniuj sekcję **sqlsink** w kodzie JSON działania kopiowania w następujący sposób. Aby wywołać procedurę składowaną podczas wstawiania danych do ujścia/docelowa baza danych, określ wartości dla właściwości **SqlWriterStoredProcedureName** i **SqlWriterTableType** . Opisy tych właściwości znajdują się [w sekcji sqlsink w artykule dotyczącym łącznika SQL Server](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definicja procedury składowanej 
W bazie danych Zdefiniuj procedurę składowaną o takiej samej nazwie jak **SqlWriterStoredProcedureName**. Procedura składowana obsługuje dane wejściowe z magazynu danych źródłowych i wstawia dane do tabeli w docelowej bazie danych. Nazwa pierwszego parametru procedury składowanej musi być zgodna z nazwą tabeli zdefiniowaną w kodzie JSON zestawu danych (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definicja typu tabeli
W swojej bazie danych Zdefiniuj typ tabeli o tej samej nazwie co **SqlWriterTableType**. Schemat typu tabeli musi być zgodny ze schematem wejściowego zestawu danych.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi łączników, które zawierają kompletne przykłady kodu JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)