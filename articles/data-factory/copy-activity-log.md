---
title: Działanie podczas kopiowania dziennika sesji
description: Dowiedz się więcej na temat włączania operacji kopiowania w dzienniku sesji w Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: c54b81ca25602fa77ad66bbb818df3cd8eee39a1
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94519976"
---
#  <a name="session-log-in-copy-activity"></a>Działanie podczas kopiowania dziennika sesji

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nazwy skopiowanych plików można rejestrować w działaniu kopiowania, co może pomóc w dokładniejszym zapewnieniu, że dane nie zostaną pomyślnie skopiowane ze źródła do magazynu docelowego, ale również spójne między magazynem źródłowym i docelowym, przeglądając skopiowane pliki w dziennikach sesji działania kopiowania.  

Po włączeniu ustawienia odporności na uszkodzenia w działaniu kopiowania w celu pominięcia uszkodzonych danych można również zarejestrować pominięte pliki i wiersze pominięte.  Możesz uzyskać więcej szczegółów z [odporności na uszkodzenia w działaniu kopiowania](copy-activity-fault-tolerance.md). 

## <a name="configuration"></a>Konfiguracja
Poniższy przykład zawiera definicję JSON umożliwiającą włączenie operacji kopiowania w dzienniku sesji: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
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
            }
        }
    }
}
```

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | Po ustawieniu na wartość true będziesz mieć możliwość rejestrowania skopiowanych plików, pominiętych plików lub pominiętych wierszy.  | Prawda<br/>False (domyślnie) | Nie
logLevel | "Informacje" będą rejestrować wszystkie skopiowane pliki, pominięte pliki i pominięte wiersze. "Ostrzeżenie" spowoduje zarejestrowanie pominiętych plików i tylko pominiętych wierszy.  | Info<br/>Ostrzeżenie (wartość domyślna) | Nie
enableReliableLogging | Gdy ma wartość true, działanie Copy w trybie niezawodnym spowoduje opróżnienie dzienników natychmiast po skopiowaniu każdego pliku do miejsca docelowego.  W przypadku kopiowania ogromnych ilości plików z włączonym niezawodnym trybem rejestrowania w działaniu kopiowania należy oczekiwać, że przepływność kopii będzie miała wpływ, ponieważ do każdego kopiowania plików są wymagane podwójne operacje zapisu. Jedno żądanie dotyczy magazynu docelowego, a inne żądanie dotyczy magazynu magazynu dzienników.  Działanie kopiowania w trybie najlepszego nakładu pracy spowoduje opróżnienie dzienników z partii rekordów w danym okresie, w którym przepływność kopii będzie znacznie mniej wpływać. Kompletność i terminowość rejestrowania nie są gwarantowane w tym trybie, ponieważ istnieje kilka możliwości, że Ostatnia Partia zdarzeń dziennika nie została opróżniona do pliku dziennika, gdy działanie kopiowania nie powiodło się. W tej chwili zobaczysz, że kilka plików skopiowanych do miejsca docelowego nie zostanie zarejestrowana.  | Prawda<br/>False (domyślnie) | Nie
logLocationSettings | Grupa właściwości, która może służyć do określania lokalizacji do przechowywania dzienników sesji. | | Nie
linkedServiceName | Połączona usługa [systemu Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) do przechowywania plików dziennika sesji. | Nazwy `AzureBlobStorage` `AzureBlobFS` połączonych usług lub typów, które odnoszą się do wystąpienia używanego do przechowywania plików dziennika. | Nie
path | Ścieżka do plików dziennika. | Określ ścieżkę, w której mają być przechowywane pliki dziennika. Jeśli nie podasz ścieżki, usługa utworzy dla Ciebie kontener. | Nie


## <a name="monitoring"></a>Monitorowanie

### <a name="output-from-copy-activity"></a>Dane wyjściowe z działania kopiowania
Po całkowitym uruchomieniu działania kopiowania można zobaczyć ścieżkę plików dziennika z danych wyjściowych każdego uruchomienia działania kopiowania. Pliki dziennika można znaleźć ze ścieżki: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Pliki dziennika będą plikami CSV. 

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

### <a name="the-schema-of-the-log-file"></a>Schemat pliku dziennika

Poniżej znajduje się Schemat pliku dziennika.

Kolumna | Opis 
-------- | -----------  
Timestamp | Sygnatura czasowa odczytywania, zapisywania lub pomijania obiektu ADF.
Poziom | Poziom dziennika tego elementu. Może to być "Warning" lub "info".
OperationName | Zachowanie działania kopiowania APD dla każdego obiektu. Może to być "FileRead", "FileWrite", "FileSkip" lub "TabularRowSkip".
OperationItem | Nazwy plików lub pominięte wiersze.
Komunikat | Więcej informacji o tym, czy plik został odczytany z magazynu źródłowego lub zapisany w magazynie docelowym. Może również być przyczyną pominięcia pliku lub wierszy.

Poniżej znajduje się przykładowy plik dziennika. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
W pliku dziennika powyżej można zobaczyć, że sample1.csv został pominięty, ponieważ nie można było zweryfikować, że jest on spójny między magazynem źródłowym i docelowym. Możesz uzyskać więcej szczegółów na temat tego, dlaczego sample1.csv stanie się niespójna, ponieważ była zmieniana przez inne aplikacje, gdy kopiowanie odbywa się w tym samym czasie. Możesz również sprawdzić, czy sample2.csv został pomyślnie skopiowany z magazynu źródłowego do docelowego.

Można użyć wielu aparatów analizy do dalszej analizy plików dziennika.  Poniżej przedstawiono kilka przykładów, aby użyć zapytania SQL do analizowania pliku dziennika przez zaimportowanie pliku dziennika CSV do bazy danych SQL, gdzie nazwa tabeli może być SessionLogDemo.  

-   Nadaj mi skopiowaną listę plików. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Podaj listę plików skopiowaną w określonym zakresie czasu. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Podaj do określonego pliku plik ze skopiowanym czasem i metadanymi. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Podaj listę plików ze swoimi metadanymi skopiowanymi w ramach przedziału czasu. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   Podaj listę pominiętych plików. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Podaj powód, dla którego określony plik został pominięty. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   Podaj listę pominiętych plików z tego samego powodu: "plik BLOB nie istnieje". 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   Podaj nazwę pliku, który wymaga najdłuższego czasu na skopiowanie.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Odporność na uszkodzenia działania kopiowania](copy-activity-fault-tolerance.md)
- [Spójność danych działania kopiowania](copy-activity-data-consistency.md)