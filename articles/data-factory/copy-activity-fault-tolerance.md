---
title: Odporność na uszkodzenia w działaniu kopiowania w usłudze Azure Data Factory
description: Dowiedz się więcej na temat sposobu dodawania odporności na uszkodzenia do działania kopiowania w Azure Data Factory, pomijając niezgodne wiersze.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417865"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Odporność na uszkodzenia w działaniu kopiowania w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Bieżąca wersja](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie Copy w Azure Data Factory oferuje dwa sposoby obsługi niezgodnych wierszy podczas kopiowania danych między źródłowym i ujściami magazynów danych:

- Działanie kopiowania można przerwać i zakończyć, gdy zostanie napotkane niezgodne dane (zachowanie domyślne).
- Możesz kontynuować kopiowanie wszystkich danych, dodając odporność na uszkodzenia i pomijając niezgodne wiersze danych. Ponadto można rejestrować niezgodne wiersze w usłudze Azure Blob Storage lub Azure Data Lake Store. Następnie można sprawdzić dziennik, aby poznać przyczynę niepowodzenia, naprawić dane w źródle danych, a następnie ponowić działanie kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Działanie Copy obsługuje trzy scenariusze wykrywania, pomijania i rejestrowania niezgodnych danych:

- **Niezgodność między typem danych źródłowych a typem natywnym ujścia**. 

    Na przykład: Skopiuj dane z pliku CSV w usłudze BLOB Storage do bazy danych SQL z definicją schematu, która zawiera trzy kolumny typu INT. Wiersze pliku CSV zawierające dane liczbowe, takie jak 123 456 789, zostały pomyślnie skopiowane do magazynu ujścia. Jednak wiersze, które zawierają wartości nieliczbowych, na przykład 123 456, ABC są wykrywane jako niezgodne i pomijane.

- **Niezgodność liczby kolumn między źródłem i ujściam**.

    Na przykład: Skopiuj dane z pliku CSV w usłudze BLOB Storage do bazy danych SQL z definicją schematu, która zawiera sześć kolumn. Wiersze pliku CSV zawierające sześć kolumn zostały pomyślnie skopiowane do magazynu ujścia. Wiersze pliku CSV zawierające więcej niż sześć kolumn są wykrywane jako niezgodne i pomijane.

- **Naruszenie klucza podstawowego podczas zapisywania do SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Na przykład: kopiowanie danych z programu SQL Server do bazy danych SQL. Klucz podstawowy jest zdefiniowany w usłudze SQL Database ujścia, ale nie jest on zdefiniowany w źródłowym programie SQL Server. Zduplikowane wiersze istniejące w źródle nie mogą zostać skopiowane do ujścia. Działanie Copy kopiuje tylko pierwszy wiersz danych źródłowych do ujścia. Kolejne wiersze źródłowe, które zawierają zduplikowaną wartość klucza podstawowego, są wykrywane jako niezgodne i pomijane.

>[!NOTE]
>- Aby można było załadować dane do SQL Data Warehouse przy użyciu bazy danych, należy skonfigurować natywne ustawienia odporności na uszkodzenia bazowe, określając odrzucanie zasad za pośrednictwem elementu "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" w działaniu kopiowania. Nadal można włączyć przekierowywanie niezgodnych wierszy podrzędnych do obiektu BLOB lub ADLS, jak pokazano poniżej.
>- Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowane do wywoływania usługi [Amazon RedShift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowane do wywoływania [procedury składowanej z ujścia bazy danych SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Konfiguracja
Poniższy przykład zawiera definicję JSON, aby skonfigurować pomijanie niezgodnych wierszy w działaniu kopiowania:

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
enableSkipIncompatibleRow | Określa, czy pomijać niezgodne wiersze podczas kopiowania. | Prawda<br/>False (domyślnie) | Nie
redirectIncompatibleRowSettings | Grupa właściwości, które można określić, gdy chcesz rejestrować niezgodne wiersze. | &nbsp; | Nie
linkedServiceName | Połączona usługa [magazynu Azure](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) do przechowywania dziennika zawierającego pominięte wiersze. | Nazwa połączonej usługi `AzureStorage` lub `AzureDataLakeStore` typu, która odwołuje się do wystąpienia, które ma być używane do przechowywania pliku dziennika. | Nie
ścieżka | Ścieżka pliku dziennika zawierającego pominięte wiersze. | Określ ścieżkę, która ma być używana do rejestrowania niezgodnych danych. Jeśli nie podasz ścieżki, usługa utworzy dla Ciebie kontener. | Nie

## <a name="monitor-skipped-rows"></a>Monitoruj pominięte wiersze
Po zakończeniu działania kopiowania można zobaczyć liczbę pominiętych wierszy w danych wyjściowych działania kopiowania:

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
W przypadku skonfigurowania rejestrowania niezgodnych wierszy można znaleźć w tej ścieżce plik dziennika: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Pliki dziennika mogą zawierać tylko pliki CSV. Oryginalne dane zostaną zarejestrowane z przecinkiem jako ogranicznik kolumny, jeśli jest to możliwe. Dodamy dwie więcej kolumn "ErrorCode" i "ErrorMessage" w dodatkowych danych źródłowych w pliku dziennika, gdzie można zobaczyć główną przyczynę niezgodności. Kod ErrorCode i ErrorMessage będą ujęte w cudzysłów. 

Przykładem zawartości pliku dziennika jest:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Wydajność działania kopiowania](copy-activity-performance.md)


