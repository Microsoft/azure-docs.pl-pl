---
title: Weryfikacja spójności danych w działaniu kopiowania
description: Dowiedz się, jak włączyć weryfikację spójności danych w działaniu kopiowania w Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: a45c8ce820532d11f18758924dc3399818cb9158
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610240"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>Weryfikacja spójności danych w działaniu kopiowania (wersja zapoznawcza)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po przeniesieniu danych ze źródła do magazynu docelowego, Azure Data Factory działanie kopiowania oferuje dodatkową weryfikację spójności danych, aby upewnić się, że dane nie zostały pomyślnie skopiowane do magazynu docelowego, ale również zweryfikowane jako zgodne z magazynem źródłowym i docelowym. Po znalezieniu niespójnych danych podczas przenoszenia danych można przerwać działanie kopiowania lub kontynuować kopiowanie reszty przez włączenie ustawienia odporności na uszkodzenia, aby pominąć niespójne dane. Pominięte nazwy obiektów można uzyskać, włączając ustawienie dziennika sesji w działaniu kopiowania. 

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej z następującymi ograniczeniami, nad którymi aktywnie pracujemy:
>- Weryfikacja spójności danych jest dostępna tylko w przypadku kopiowania plików binarnych między magazynami opartymi na plikach z zachowaniem "PreserveHierarchy" w działaniu kopiowania. W przypadku kopiowania danych tabelarycznych Weryfikacja spójności danych nie jest jeszcze dostępna w działaniu kopiowania.
>- Po włączeniu ustawienia dziennika sesji w działaniu kopiowania do rejestrowania niespójnych plików, kompletność pliku dziennika nie może być 100% gwarantowane, jeśli działanie kopiowania nie powiodło się.
>- Dziennik sesji zawiera tylko niespójne pliki, w przypadku których pomyślnie skopiowane pliki nie są do tej pory rejestrowane.

## <a name="supported-data-stores"></a>Obsługiwane magazyny danych

### <a name="source-data-stores"></a>Źródłowe magazyny danych

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [System plików](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>Docelowe magazyny danych

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [System plików](connector-file-system.md)


## <a name="configuration"></a>Konfiguracja
Poniższy przykład zawiera definicję JSON, aby włączyć weryfikację spójności danych w działaniu kopiowania: 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | -------- 
validateDataConsistency | W przypadku ustawienia wartości true dla tej właściwości działanie Copy sprawdzi rozmiar pliku, lastModifiedDate i sumę kontrolną MD5 dla każdego obiektu skopiowanego ze źródła do magazynu docelowego, aby zapewnić spójność danych między magazynem źródłowym a docelowym. Należy pamiętać, że ta opcja będzie miała wpływ na wydajność kopiowania.  | True<br/>False (domyślnie) | Nie
dataInconsistency | Jeden z par klucz-wartość w zbiorze właściwości skipErrorFile, aby określić, czy chcesz pominąć niespójne dane.<br/> -True: chcesz skopiować resztę, pomijając niespójne dane.<br/> -False: chcesz przerwać działanie kopiowania po znalezieniu niespójnych danych.<br/>Należy pamiętać, że właściwość jest prawidłowa tylko po ustawieniu validateDataConsistency jako true.  | True<br/>False (domyślnie) | Nie
logStorageSettings | Grupa właściwości, które można określić, aby umożliwić dziennikowi sesji rejestrowanie pominiętych obiektów. | | Nie
linkedServiceName | Połączona usługa [systemu Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) do przechowywania plików dziennika sesji. | Nazwy `AzureBlobStorage` `AzureBlobFS` połączonych usług lub typów, które odnoszą się do wystąpienia używanego do przechowywania plików dziennika. | Nie
ścieżka | Ścieżka do plików dziennika. | Określ ścieżkę, w której mają być przechowywane pliki dziennika. Jeśli nie podasz ścieżki, usługa utworzy dla Ciebie kontener. | Nie

>[!NOTE]
>- Spójność danych nie jest obsługiwana w scenariuszu kopiowania przemieszczania. 
>- Podczas kopiowania plików z, lub do obiektu blob platformy Azure lub Azure Data Lake Storage Gen2, usługa ADF jest zgodna z weryfikacją sum kontrolnych MD5 poziomu blokowego na podstawie [interfejsu API usługi Azure Blob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy) i [interfejsu API Azure Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update#request-headers) Jeśli ContentMD5 w plikach istnieje w obiekcie blob platformy Azure lub Azure Data Lake Storage Gen2 jako źródła danych, na AUTOMATYCZNYm przeczytaniu plików zostanie zweryfikowana suma kontrolna MD5 na poziomie pliku. Po skopiowaniu plików do obiektu blob platformy Azure lub Azure Data Lake Storage Gen2 jako miejsce docelowe danych usługa ADF zapisuje ContentMD5 do obiektu blob platformy Azure lub Azure Data Lake Storage Gen2, które mogą być dodatkowo używane przez aplikacje podrzędne do weryfikacji spójności danych.
>- Usługa ADF wykonuje weryfikację rozmiaru plików podczas kopiowania plików między dowolnymi magazynami magazynu.

## <a name="monitoring"></a>Monitorowanie

### <a name="output-from-copy-activity"></a>Dane wyjściowe z działania kopiowania
Po całkowitym uruchomieniu działania kopiowania można zobaczyć wynik weryfikacji spójności danych z danych wyjściowych poszczególnych przebiegów działania kopiowania:

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```
Szczegóły weryfikacji spójności danych można zobaczyć ze "właściwością dataConsistencyVerification".

Wartość **VerificationResult**: 
-   **Zweryfikowano**: skopiowane dane zostały zweryfikowane pod kątem spójności między magazynem źródłowym i docelowym. 
-   **NotVerified**: skopiowane dane nie zostały zweryfikowane pod kątem spójności, ponieważ nie włączono validateDataConsistency w działaniu kopiowania. 
-   **Nieobsługiwane**: skopiowane dane nie zostały zweryfikowane pod kątem spójności, ponieważ Weryfikacja spójności danych nie jest obsługiwana dla danej pary kopiowania. 

Wartość **InconsistentData**: 
-   **Znaleziono**: działanie kopiowania ADF wykryło niespójne dane. 
-   **Pominięto**: działanie kopiowania APD zostało znalezione i pominięto niespójne dane. 
-   **Brak**: działanie kopiowania APD nie znalazło żadnych niespójnych danych. Może to być spowodowane faktem, że dane zostały zweryfikowane w celu zapewnienia spójności między magazynem źródłowym i docelowym lub ponieważ wyłączono validateDataConsistency w działaniu kopiowania. 

### <a name="session-log-from-copy-activity"></a>Dziennik sesji z działania kopiowania

W przypadku skonfigurowania programu w celu rejestrowania niespójnego pliku można znaleźć plik dziennika z tej ścieżki: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Pliki dziennika będą plikami CSV. 

Schemat pliku dziennika jest następujący:

Kolumna | Opis 
-------- | -----------  
Znacznik czasu | Sygnatura czasowa, gdy moduł ADF pomija niespójne pliki.
Poziom | Poziom dziennika tego elementu. Będzie on wyświetlany na poziomie "ostrzeżenie" dla elementu pokazującego pomijanie plików.
OperationName | Zachowanie działania kopiowania APD dla każdego pliku. Będzie to "FileSkip", aby określić plik do pominięcia.
OperationItem | Nazwa pliku do pominięcia.
Komunikat | Więcej informacji o tym, dlaczego pliki są pomijane.

Przykładowy plik dziennika jest następujący: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
W pliku dziennika powyżej można zobaczyć, że sample1. csv został pominięty, ponieważ nie można było zweryfikować, że jest on spójny między magazynem źródłowym i docelowym. Możesz uzyskać więcej szczegółów na temat tego, dlaczego sample1. csv stanie się niespójny, ponieważ został zmieniony przez inne aplikacje, gdy kopiowanie odbywa się w tym samym czasie. 



## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Odporność na uszkodzenia działania kopiowania](copy-activity-fault-tolerance.md)


