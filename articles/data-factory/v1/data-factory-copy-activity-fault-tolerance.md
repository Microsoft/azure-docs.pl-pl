---
title: Dodaj tolerancję błędów w działaniu Azure Data Factory kopiowania, pomijając niezgodne wiersze
description: Dowiedz się, jak dodać odporność na uszkodzenia w Azure Data Factory działania kopiowania, pomijając niezgodne wiersze podczas kopiowania
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 10e4bedae5b7c429152a3503fff2cb2769d66eb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377180"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Dodaj odporność na uszkodzenia w działaniu kopiowania, pomijając niezgodne wiersze

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-copy-activity-fault-tolerance.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [odporność na uszkodzenia w działaniu kopiowania Data Factory](../copy-activity-fault-tolerance.md).

Azure Data Factory [działanie kopiowania](data-factory-data-movement-activities.md) oferuje dwa sposoby obsługi niezgodnych wierszy podczas kopiowania danych między magazynem danych źródłowych i ujściami:

- Działanie kopiowania można przerwać i zakończyć, gdy zostanie napotkane niezgodne dane (zachowanie domyślne).
- Możesz kontynuować kopiowanie wszystkich danych, dodając odporność na uszkodzenia i pomijając niezgodne wiersze danych. Ponadto można rejestrować niezgodne wiersze w usłudze Azure Blob Storage. Następnie można sprawdzić dziennik, aby poznać przyczynę niepowodzenia, naprawić dane w źródle danych, a następnie ponowić działanie kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Działanie Copy obsługuje trzy scenariusze wykrywania, pomijania i rejestrowania niezgodnych danych:

- **Niezgodność typu danych źródłowych z natywnym typem ujścia**

    Na przykład: Skopiuj dane z pliku CSV w usłudze BLOB Storage do bazy danych SQL z definicją schematu, która zawiera trzy kolumny typu **int** . Wiersze pliku CSV zawierające dane liczbowe, takie jak `123,456,789` są kopiowane pomyślnie do magazynu ujścia. Jednak wiersze, które zawierają wartości inne niż liczbowe, takie jak `123,456,abc` są wykryty jako niezgodne i są pomijane.

- **Niezgodność liczby kolumn między źródłem i ujściem**

    Na przykład: Skopiuj dane z pliku CSV w usłudze BLOB Storage do bazy danych SQL z definicją schematu, która zawiera sześć kolumn. Wiersze pliku CSV zawierające sześć kolumn zostały pomyślnie skopiowane do magazynu ujścia. Wiersze pliku CSV zawierające więcej lub mniej niż sześć kolumn są wykrywane jako niezgodne i pomijane.

- **Naruszenie klucza podstawowego podczas zapisywania w usłudze SQL Server/Azure SQL Database/Azure Cosmos DB**

    Na przykład: kopiowanie danych z programu SQL Server do bazy danych SQL. Klucz podstawowy jest zdefiniowany w usłudze SQL Database ujścia, ale nie jest on zdefiniowany w źródłowym programie SQL Server. Zduplikowane wiersze istniejące w źródle nie mogą zostać skopiowane do ujścia. Działanie Copy kopiuje tylko pierwszy wiersz danych źródłowych do ujścia. Kolejne wiersze źródłowe, które zawierają zduplikowaną wartość klucza podstawowego, są wykrywane jako niezgodne i pomijane.

>[!NOTE]
>Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowane do wywoływania zewnętrznego mechanizmu ładowania danych, w tym [usługi Azure Synapse Analytics Base](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) lub z funkcji [Amazon RedShift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Aby załadować dane do usługi Azure Synapse Analytics przy użyciu bazy danych Base, należy użyć natywnej obsługi odporności na uszkodzenia bazy. w tym celu należy określić wartość "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" w działaniu kopiowania.

## <a name="configuration"></a>Konfigurowanie
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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Włącz pomijanie niezgodnych wierszy podczas kopiowania lub nie. | Prawda<br/>False (domyślnie) | Nie |
| **redirectIncompatibleRowSettings** | Grupa właściwości, które można określić, gdy chcesz rejestrować niezgodne wiersze. | &nbsp; | Nie |
| **linkedServiceName** | Połączona usługa usługi Azure Storage do przechowywania dziennika zawierającego pominięte wiersze. | Nazwa połączonej usługi [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) , która odwołuje się do wystąpienia magazynu, które ma być używane do przechowywania pliku dziennika. | Nie |
| **ścieżka** | Ścieżka pliku dziennika zawierającego pominięte wiersze. | Określ ścieżkę magazynu obiektów blob, która ma być używana do rejestrowania niezgodnych danych. Jeśli nie podasz ścieżki, usługa utworzy dla Ciebie kontener. | Nie |

## <a name="monitoring"></a>Monitorowanie
Po zakończeniu działania kopiowania można zobaczyć liczbę pominiętych wierszy w sekcji Monitorowanie:

![Monitor pominięto niezgodne wiersze](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Jeśli skonfigurujesz rejestrowanie niezgodnych wierszy, plik dziennika można znaleźć w tej ścieżce: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` w pliku dziennika można zobaczyć wiersze, które zostały pominięte, oraz główną przyczynę niezgodności.

Zarówno oryginalne dane, jak i odpowiadające im błędy są rejestrowane w pliku. Przykładem zawartości pliku dziennika jest:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat działania kopiowania Azure Data Factory, zobacz [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md).
