---
title: Odporność na uszkodzenia w działaniu kopiowania w usłudze Azure Data Factory
description: Dowiedz się, jak dodać odporność na uszkodzenia, aby skopiować działanie w usłudze Azure Data Factory, pomijając niezgodne wiersze.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: 766520fe44047eee76029adf8ee1683c7b8008a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417865"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Odporność na uszkodzenia w działaniu kopiowania w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Bieżąca wersja](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie kopiowania w usłudze Azure Data Factory oferuje dwa sposoby obsługi niezgodnych wierszy podczas kopiowania danych między magazynami danych źródłowych i magazynów danych ujścia:

- Działanie kopiowania można przerwać i zakończyć niepowodzeniem w przypadku wystąpienia niezgodnych danych (zachowanie domyślne).
- Można kontynuować kopiowanie wszystkich danych, dodając odporność na uszkodzenia i pomijając niezgodne wiersze danych. Ponadto można rejestrować niezgodne wiersze w magazynie obiektów Blob platformy Azure lub usługi Azure Data Lake Store. Następnie można sprawdzić dziennik, aby dowiedzieć się przyczyny błędu, naprawić dane w źródle danych i ponów próbę wykonania działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Działanie kopiowania obsługuje trzy scenariusze wykrywania, pomijania i rejestrowania niezgodnych danych:

- **Niezgodność między typem danych źródłowych a typem macierzystym ujścia**. 

    Na przykład: Skopiuj dane z pliku CSV w magazynie obiektów Blob do bazy danych SQL z definicją schematu zawierającą trzy kolumny typu INT. Wiersze plików CSV zawierające dane liczbowe, takie jak 123 456 789, są pomyślnie kopiowane do magazynu ujścia. Jednak wiersze, które zawierają wartości nienumeryczne, takie jak 123,456, abc są wykrywane jako niezgodne i są pomijane.

- **Niezgodność w liczbie kolumn między źródłem a ujściem**.

    Na przykład: Skopiuj dane z pliku CSV w magazynie obiektów Blob do bazy danych SQL z definicją schematu zawierającą sześć kolumn. Wiersze plików CSV, które zawierają sześć kolumn są kopiowane pomyślnie do magazynu ujścia. Wiersze pliku CSV, które zawierają więcej niż sześć kolumn są wykrywane jako niezgodne i pomijane.

- **Naruszenie klucza podstawowego podczas zapisywania do programu SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Na przykład: Kopiowanie danych z serwera SQL do bazy danych SQL. Klucz podstawowy jest zdefiniowany w bazie danych lokacji SQL, ale nie taki klucz podstawowy nie jest zdefiniowany w źródłowym serwerze SQL. Zduplikowanych wierszy, które istnieją w źródle nie można skopiować do ujścia. Kopiuj działanie kopiuje tylko pierwszy wiersz danych źródłowych do ujścia. Kolejne wiersze źródłowe zawierające zduplikowaną wartość klucza podstawowego są wykrywane jako niezgodne i pomijane.

>[!NOTE]
>- Aby załadować dane do magazynu danych SQL przy użyciu polybase, skonfiguruj natywne ustawienia tolerancji błędów PolyBase, określając zasady odrzucania za pomocą "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" w działaniu kopiowania. Nadal można włączyć przekierowywanie PolyBase niezgodne wiersze do obiektów Blob lub ADLS jak zwykle, jak pokazano poniżej.
>- Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowane do wywoływania [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowane do wywoływania [procedury składowanej z ujścia SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Konfigurowanie
Poniższy przykład zawiera definicję JSON, aby skonfigurować pomijanie niezgodnych wierszy w copy activity:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Określa, czy nie można pominąć niezgodnych wierszy podczas kopiowania, czy nie. | True<br/>False (domyślnie) | Nie
redirectIncompatibleRowSettings | Grupa właściwości, które można określić, gdy chcesz zarejestrować niezgodne wiersze. | &nbsp; | Nie
linkedServiceName | Połączona usługa [usługi Azure Storage](connector-azure-blob-storage.md#linked-service-properties) lub Azure Data Lake [Store](connector-azure-data-lake-store.md#linked-service-properties) do przechowywania dziennika, który zawiera pominięte wiersze. | Nazwa usługi `AzureStorage` połączonej lub `AzureDataLakeStore` typu, która odwołuje się do wystąpienia, którego chcesz użyć do przechowywania pliku dziennika. | Nie
ścieżka | Ścieżka pliku dziennika zawierającego pominięte wiersze. | Określ ścieżkę, której chcesz użyć do rejestrowania niezgodnych danych. Jeśli nie podasz ścieżkę, usługa tworzy kontener dla Ciebie. | Nie

## <a name="monitor-skipped-rows"></a>Monitoruj pominięte wiersze
Po zakończeniu wykonywania działania kopiowania można zobaczyć liczbę pominiętych wierszy w danych wyjściowych działania kopiowania:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Jeśli skonfigurujesz rejestrowanie niezgodnych wierszy, plik dziennika można `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`znaleźć w tej ścieżce: . 

Pliki dziennika mogą być tylko plikami csv. Oryginalne dane pomijane będą rejestrowane przecinkiem jako ogranicznik kolumny w razie potrzeby. Dodajemy jeszcze dwie kolumny "ErrorCode" i "ErrorMessage" w uzupełnieniach do oryginalnych danych źródłowych w pliku dziennika, gdzie można zobaczyć główną przyczynę niezgodności. ErrorCode i ErrorMessage będą cytowane przez podwójne cudzysłowy. 

Przykład zawartości pliku dziennika jest następujący:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Następne kroki
Zobacz inne artykuły dotyczące działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Wydajność działania kopiowania](copy-activity-performance.md)


