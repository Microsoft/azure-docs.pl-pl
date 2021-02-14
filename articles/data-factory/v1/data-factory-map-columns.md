---
title: Mapowanie kolumn zestawu danych w Azure Data Factory
description: Dowiedz się, jak mapować kolumny źródłowe na kolumny docelowe.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fef2c6f120ae25e6aa1846d4971ff707da9bab92
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371128"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapowanie kolumn źródłowych zestawów danych do docelowych kolumn zestawu danych
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Mapowania kolumn można użyć do określenia sposobu, w jaki kolumny określone w "strukturze" tabeli źródłowej są mapowane do kolumn określonych w "strukturze" tabeli ujścia. Właściwość **ColumnMapping** jest dostępna w sekcji **typeProperties** działania kopiowania.

Mapowanie kolumn obsługuje następujące scenariusze:

* Wszystkie kolumny w strukturze zestawu danych źródłowych są mapowane na wszystkie kolumny w strukturze zestawu danych ujścia.
* Podzestaw kolumn w strukturze zestawu danych źródłowych jest mapowany na wszystkie kolumny w strukturze zestawu danych ujścia.

Poniżej przedstawiono warunki błędów, które powodują wyjątek:

* Mniejsza liczba kolumn lub więcej kolumn w strukturze "struktura" tabeli ujścia niż określona w mapowaniu.
* Zduplikowane mapowanie.
* Wynik zapytania SQL nie ma nazwy kolumny, która jest określona w mapowaniu.

> [!NOTE]
> Poniższe przykłady są przeznaczone dla usług Azure SQL i Azure Blob, ale mają zastosowanie do każdego magazynu danych, który obsługuje prostokątne zestawy DataSet. Dostosuj definicje zestawu danych i połączonej usługi w przykładach, aby wskazać dane w odpowiednim źródle danych.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Przykład 1 — Mapowanie kolumn z usługi Azure SQL do obiektu blob platformy Azure
W tym przykładzie tabela wejściowa ma strukturę i wskazuje tabelę SQL w Azure SQL Database.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

W tym przykładzie tabela wyjściowa ma strukturę i wskazuje na obiekt BLOB w magazynie obiektów blob platformy Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Poniższy kod JSON definiuje działanie kopiowania w potoku. Kolumny ze źródła zamapowane na kolumny w usłudze sink (**ColumnMappings**) za pomocą właściwości **translator** .

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Przepływ mapowania kolumn:**

![Przepływ mapowania kolumn](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Przykład 2 — Mapowanie kolumn za pomocą zapytania SQL z usługi Azure SQL do obiektu blob platformy Azure
W tym przykładzie zapytanie SQL służy do wyodrębniania danych z usługi Azure SQL, a nie do zwykłego określania nazwy tabeli i nazw kolumn w sekcji "Structure". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
W takim przypadku wyniki zapytania są najpierw mapowane do kolumn określonych w "strukturze" źródła. Następnie kolumny ze źródła "Structure" są mapowane na kolumny w ujścia "Structure" z regułami określonymi w kolumnie columnMappings.  Załóżmy, że zapytanie zwraca 5 kolumn, dwie więcej kolumn niż określone w "strukturze" źródła.

**Przepływ mapowania kolumn**

![Przepływ mapowania kolumn — 2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z samouczkiem dotyczącym korzystania z działania kopiowania: 

- [Kopiuj dane z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
