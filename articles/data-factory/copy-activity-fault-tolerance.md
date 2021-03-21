---
title: Odporność na uszkodzenia w działaniu kopiowania w usłudze Azure Data Factory
description: Dowiedz się więcej na temat sposobu dodawania odporności na uszkodzenia do działania kopiowania w Azure Data Factory, pomijając niezgodne dane.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fe1470661c006399ea176af1112d271524b2a1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390967"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Odporność na uszkodzenia w działaniu kopiowania w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Bieżąca wersja](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W przypadku kopiowania danych ze źródła do magazynu docelowego Azure Data Factory działania kopiowania zapewniają pewien poziom odporności na uszkodzenia, aby zapobiec przerwom w występowaniu błędów w trakcie przenoszenia danych. Na przykład kopiowane są miliony wierszy ze źródła do magazynu docelowego, gdzie klucz podstawowy został utworzony w docelowej bazie danych, ale źródłowa baza danych nie ma zdefiniowanych kluczy podstawowych. Gdy kopiujesz zduplikowane wiersze z lokalizacji źródłowej do docelowej, wystąpi błąd naruszenia klucza podstawowego dla docelowej bazy danych. W tej chwili działanie kopiowania oferuje dwa sposoby obsługi takich błędów: 
- Działanie kopiowania można przerwać po wystąpieniu dowolnego błędu. 
- Aby pominąć niezgodne dane, można kontynuować kopiowanie reszty przez włączenie funkcji odporność na uszkodzenia. Na przykład Pomiń zduplikowany wiersz w tym przypadku. Ponadto można rejestrować pominięte dane, włączając dziennik sesji w ramach działania kopiowania. Aby uzyskać więcej informacji, można odwołać się do [dziennika sesji w działaniu kopiowania](copy-activity-log.md) .

## <a name="copying-binary-files"></a>Kopiowanie plików binarnych 

ADF obsługuje następujące scenariusze odporności na uszkodzenia podczas kopiowania plików binarnych. Możesz przerwać działanie kopiowania lub kontynuować kopiowanie reszty w następujących scenariuszach:

1. Pliki do skopiowania przez ADF są usuwane przez inne aplikacje w tym samym czasie.
2. Niektóre konkretne foldery lub pliki nie zezwalają na dostęp do usługi ADF, ponieważ listy ACL tych plików lub folderów wymagają wyższego poziomu uprawnień niż informacje o połączeniu skonfigurowane w podajniku ADF.
3. Co najmniej jeden plik nie jest weryfikowany pod kątem spójności między magazynem źródłowym i docelowym w przypadku włączenia ustawienia weryfikacji spójności danych w ADF.

### <a name="configuration"></a>Konfigurowanie 
Podczas kopiowania plików binarnych między magazynami magazynów można włączyć odporność na uszkodzenia w następujący sposób: 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true        
    }, 
    "validateDataConsistency": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
} 
```
Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | -------- 
skipErrorFile | Grupa właściwości, aby określić typy błędów, które mają zostać pominięte podczas przenoszenia danych. | | Nie
fileMissing | Jeden z par klucz-wartość w zbiorze właściwości skipErrorFile, aby określić, czy chcesz pominąć pliki, które są usuwane przez inne aplikacje, gdy w tym czasie jest kopiowany ADF. <br/> -True: chcesz skopiować resztę, pomijając pliki usuwane przez inne aplikacje. <br/> -False: chcesz przerwać działanie kopiowania po usunięciu plików ze sklepu źródłowego w trakcie przenoszenia danych. <br/>Należy pamiętać, że właściwość jest ustawiona na wartość true jako domyślną. | True (domyślnie) <br/>Fałsz | Nie
fileForbidden | Jedna z par klucz-wartość w zbiorze właściwości skipErrorFile, aby określić, czy chcesz pominąć określone pliki, gdy listy kontroli dostępu do tych plików lub folderów wymagają wyższego poziomu uprawnień niż połączenie skonfigurowane w podajniku ADF. <br/> -True: chcesz skopiować resztę, pomijając pliki. <br/> -False: chcesz przerwać działanie kopiowania po pobraniu problemu z uprawnieniami do folderów lub plików. | Prawda <br/>False (domyślnie) | Nie
dataInconsistency | Jedna z par klucz-wartość w zbiorze właściwości skipErrorFile, aby określić, czy chcesz pominąć niespójne dane między magazynem źródłowym i docelowym. <br/> -True: chcesz skopiować resztę, pomijając niespójne dane. <br/> -False: chcesz przerwać działanie kopiowania po znalezieniu niespójnych danych. <br/>Należy pamiętać, że właściwość jest prawidłowa tylko po ustawieniu validateDataConsistency jako true. | Prawda <br/>False (domyślnie) | Nie
invalidFileName | Jeden z par klucz-wartość w zbiorze właściwości skipErrorFile, aby określić, czy chcesz pominąć określone pliki, gdy nazwy plików są nieprawidłowe dla magazynu docelowego. <br/> -True: chcesz skopiować resztę, pomijając pliki z nieprawidłowymi nazwami plików. <br/> -False: chcesz przerwać działanie kopiowania, gdy wszystkie pliki mają nieprawidłową nazwę pliku. <br/>Należy pamiętać, że ta właściwość działa podczas kopiowania plików binarnych z dowolnego magazynu magazynu do ADLS Gen2 lub kopiowania plików binarnych z AWS S3 do dowolnego magazynu magazynu. | Prawda <br/>False (domyślnie) | Nie
logSettings  | Grupa właściwości, które można określić, gdy mają być rejestrowane pominięte nazwy obiektów. | &nbsp; | Nie
linkedServiceName | Połączona usługa [systemu Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) do przechowywania plików dziennika sesji. | Nazwy `AzureBlobStorage` `AzureBlobFS` połączonej usługi lub typu, która odnosi się do wystąpienia używanego do przechowywania pliku dziennika. | Nie
path | Ścieżka do plików dziennika. | Określ ścieżkę, która ma być używana do przechowywania plików dziennika. Jeśli nie podasz ścieżki, usługa utworzy dla Ciebie kontener. | Nie

> [!NOTE]
> Poniżej przedstawiono wymagania wstępne dotyczące włączania odporności błędów w działaniu kopiowania podczas kopiowania plików binarnych.
> W przypadku pomijania określonych plików, gdy są usuwane z magazynu źródłowego:
> - Źródłowy zestaw danych i zestaw danych ujścia muszą mieć format binarny i nie można określić typu kompresji. 
> - Obsługiwane typy magazynów danych to Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, system plików, FTP, SFTP, Amazon S3, Google Cloud Storage i HDFS.
> - W przypadku określenia wielu plików w źródłowym zestawie danych, które mogą być folderem, symbolem wieloznacznym lub listą plików, działanie Copy może pominąć określone pliki błędów. Jeśli jeden plik zostanie określony w źródłowym zestawie danych do skopiowania do miejsca docelowego, działanie kopiowania zakończy się niepowodzeniem, jeśli wystąpił błąd.
>
> W przypadku pomijania określonych plików, gdy ich dostęp jest zabroniony z magazynu źródłowego:
> - Źródłowy zestaw danych i zestaw danych ujścia muszą mieć format binarny i nie można określić typu kompresji. 
> - Obsługiwane typy magazynów danych to Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, SFTP, Amazon S3 i HDFS.
> - W przypadku określenia wielu plików w źródłowym zestawie danych, które mogą być folderem, symbolem wieloznacznym lub listą plików, działanie Copy może pominąć określone pliki błędów. Jeśli jeden plik zostanie określony w źródłowym zestawie danych do skopiowania do miejsca docelowego, działanie kopiowania zakończy się niepowodzeniem, jeśli wystąpił błąd.
>
> W przypadku pomijania określonych plików, gdy są one zweryfikowane jako niespójne między magazynem źródłowym i docelowym:
> - W [tym miejscu](./copy-activity-data-consistency.md)możesz uzyskać więcej szczegółowych informacji z dokumentu spójności danych.

### <a name="monitoring"></a>Monitorowanie 

#### <a name="output-from-copy-activity"></a>Dane wyjściowe z działania kopiowania
Można uzyskać liczbę plików odczytywanych, zapisywanych i pomijanych za pośrednictwem danych wyjściowych każdego uruchomienia działania kopiowania. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>Dziennik sesji z działania kopiowania

Jeśli skonfigurujesz program do rejestrowania pominiętych nazw plików, plik dziennika można znaleźć w tej ścieżce: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Pliki dziennika muszą być plikami CSV. Schemat pliku dziennika jest następujący:

Kolumna | Opis 
-------- | -----------  
Znacznik czasu | Sygnatura czasowa w przypadku pomijania pliku przez funkcję ADF.
Poziom | Poziom dziennika tego elementu. Będzie on wyświetlany na poziomie "ostrzeżenie" dla elementu pokazującego pomijanie plików.
OperationName | Zachowanie działania kopiowania APD dla każdego pliku. Będzie to "FileSkip", aby określić plik do pominięcia.
OperationItem | Nazwy plików, które mają zostać pominięte.
Komunikat | Więcej informacji o tym, dlaczego pominięto plik.

Przykładowy plik dziennika jest następujący: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
W powyższym dzienniku można zobaczyć, że bigfile.csv został pominięty z powodu usunięcia przez inną aplikację tego pliku, gdy plik ADF skopiował go. I 3_nopermission.txt został pominięty, ponieważ ADF nie może uzyskać do niego dostępu z powodu problemu z uprawnieniami.


## <a name="copying-tabular-data"></a>Kopiowanie danych tabelarycznych 

### <a name="supported-scenarios"></a>Obsługiwane scenariusze
Działanie Copy obsługuje trzy scenariusze wykrywania, pomijania i rejestrowania niezgodnych danych tabelarycznych:

- **Niezgodność między typem danych źródłowych a typem natywnym ujścia**. 

    Na przykład: Skopiuj dane z pliku CSV w usłudze BLOB Storage do bazy danych SQL z definicją schematu, która zawiera trzy kolumny typu INT. Wiersze pliku CSV zawierające dane liczbowe, takie jak 123 456 789, zostały pomyślnie skopiowane do magazynu ujścia. Jednak wiersze, które zawierają wartości nieliczbowych, na przykład 123 456, ABC są wykrywane jako niezgodne i pomijane.

- **Niezgodność liczby kolumn między źródłem i ujściam**.

    Na przykład: Skopiuj dane z pliku CSV w usłudze BLOB Storage do bazy danych SQL z definicją schematu, która zawiera sześć kolumn. Wiersze pliku CSV zawierające sześć kolumn zostały pomyślnie skopiowane do magazynu ujścia. Wiersze pliku CSV zawierające więcej niż sześć kolumn są wykrywane jako niezgodne i pomijane.

- **Naruszenie klucza podstawowego podczas zapisywania do SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Na przykład: kopiowanie danych z programu SQL Server do bazy danych SQL. Klucz podstawowy jest zdefiniowany w usłudze SQL Database ujścia, ale nie jest on zdefiniowany w źródłowym programie SQL Server. Zduplikowane wiersze istniejące w źródle nie mogą zostać skopiowane do ujścia. Działanie Copy kopiuje tylko pierwszy wiersz danych źródłowych do ujścia. Kolejne wiersze źródłowe, które zawierają zduplikowaną wartość klucza podstawowego, są wykrywane jako niezgodne i pomijane.

>[!NOTE]
>- Aby załadować dane do usługi Azure Synapse Analytics przy użyciu bazy danych, należy skonfigurować natywne ustawienia odporności na uszkodzenia na podstawie, określając odrzucanie zasad za pośrednictwem elementu "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" w działaniu kopiowania. Nadal można włączyć przekierowywanie niezgodnych wierszy podrzędnych do obiektu BLOB lub ADLS, jak pokazano poniżej.
>- Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowane do wywoływania usługi [Amazon RedShift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Ta funkcja nie ma zastosowania, gdy działanie kopiowania jest skonfigurowane do wywoływania [procedury składowanej z ujścia bazy danych SQL](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink).

### <a name="configuration"></a>Konfigurowanie
Poniższy przykład zawiera definicję JSON, aby skonfigurować pomijanie niezgodnych wierszy w działaniu kopiowania:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    } 
}, 
```

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Określa, czy pomijać niezgodne wiersze podczas kopiowania. | Prawda<br/>False (domyślnie) | Nie
logSettings | Grupa właściwości, które można określić, gdy chcesz rejestrować niezgodne wiersze. | &nbsp; | Nie
linkedServiceName | Połączona usługa [systemu Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) do przechowywania dziennika zawierającego pominięte wiersze. | Nazwy `AzureBlobStorage` `AzureBlobFS` połączonej usługi lub typu, która odnosi się do wystąpienia używanego do przechowywania pliku dziennika. | Nie
path | Ścieżka do plików dziennika, która zawiera pominięte wiersze. | Określ ścieżkę, która ma być używana do rejestrowania niezgodnych danych. Jeśli nie podasz ścieżki, usługa utworzy dla Ciebie kontener. | Nie

### <a name="monitor-skipped-rows"></a>Monitoruj pominięte wiersze
Po zakończeniu działania kopiowania można zobaczyć liczbę pominiętych wierszy w danych wyjściowych działania kopiowania:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Jeśli skonfigurujesz rejestrowanie niezgodnych wierszy, możesz znaleźć plik dziennika z tej ścieżki: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Pliki dziennika będą plikami CSV. Schemat pliku dziennika jest następujący:

Kolumna | Opis 
-------- | -----------  
Znacznik czasu | Sygnatura czasowa, gdy funkcja ADF pomija niezgodne wiersze
Poziom | Poziom dziennika tego elementu. Jeśli ten element wyświetli pominięte wiersze, będzie działać na poziomie "ostrzeżenie"
OperationName | Zachowanie działania kopiowania APD dla każdego wiersza. Będzie to "TabularRowSkip", aby określić, że konkretny niezgodny wiersz został pominięty
OperationItem | Pominięte wiersze z magazynu danych źródłowych.
Komunikat | Więcej informacji ilustrujących przyczynę niezgodności tego konkretnego wiersza.


Przykładem zawartości pliku dziennika jest:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

Z przykładowego pliku dziennika można zobaczyć jeden wiersz "dane1, Dane2, DATA3" został pominięty z powodu problemu konwersji typu z magazynu źródłowego na docelowy. Inny wiersz "DATA4, DATA5, data6" został pominięty z powodu problemu z naruszeniem klucza podstawowego ze źródła do magazynu docelowego. 


## <a name="copying-tabular-data-legacy"></a>Kopiowanie danych tabelarycznych (starsza wersja):

Poniżej znajduje się starszy sposób włączania odporności na uszkodzenia tylko do kopiowania danych tabelarycznych. W przypadku tworzenia nowego potoku lub działania zaleca się rozpoczęcie od tego [miejsca](#copying-tabular-data) .

### <a name="configuration"></a>Konfigurowanie
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

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Określa, czy pomijać niezgodne wiersze podczas kopiowania. | Prawda<br/>False (domyślnie) | Nie
redirectIncompatibleRowSettings | Grupa właściwości, które można określić, gdy chcesz rejestrować niezgodne wiersze. | &nbsp; | Nie
linkedServiceName | Połączona usługa [magazynu Azure](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) do przechowywania dziennika zawierającego pominięte wiersze. | Nazwy `AzureStorage` `AzureDataLakeStore` połączonej usługi lub typu, która odwołuje się do wystąpienia, które ma być używane do przechowywania pliku dziennika. | Nie
path | Ścieżka pliku dziennika zawierającego pominięte wiersze. | Określ ścieżkę, która ma być używana do rejestrowania niezgodnych danych. Jeśli nie podasz ścieżki, usługa utworzy dla Ciebie kontener. | Nie

### <a name="monitor-skipped-rows"></a>Monitoruj pominięte wiersze
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
W przypadku skonfigurowania rejestrowania niezgodnych wierszy można znaleźć w tej ścieżce plik dziennika: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

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