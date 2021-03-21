---
title: Weryfikacja spójności danych w działaniu kopiowania
description: Dowiedz się, jak włączyć weryfikację spójności danych w działaniu kopiowania w Azure Data Factory.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: b71657f67c1b9c623d6d48f33b986ac43533cca6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373020"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Weryfikacja spójności danych w działaniu kopiowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po przeniesieniu danych ze źródła do magazynu docelowego, Azure Data Factory działanie kopiowania oferuje dodatkową weryfikację spójności danych, aby upewnić się, że dane nie zostały pomyślnie skopiowane do magazynu docelowego, ale również zweryfikowane jako zgodne z magazynem źródłowym i docelowym. Po znalezieniu niespójnych plików podczas przenoszenia danych można przerwać działanie kopiowania lub kontynuować kopiowanie reszty przez włączenie ustawienia odporności na uszkodzenia, aby pominąć niespójne pliki. Pominięte nazwy plików można uzyskać, włączając ustawienie dziennika sesji w działaniu kopiowania. Aby uzyskać więcej informacji, można odwołać się do [dziennika sesji w działaniu kopiowania](copy-activity-log.md) .

## <a name="supported-data-stores-and-scenarios"></a>Obsługiwane magazyny danych i scenariusze

-   Weryfikacja spójności danych jest obsługiwana przez wszystkie łączniki z wyjątkiem FTP, sFTP i HTTP. 
-   Weryfikacja spójności danych nie jest obsługiwana w scenariuszu kopiowania przemieszczania.
-   Podczas kopiowania plików binarnych Weryfikacja spójności danych jest dostępna tylko wtedy, gdy w działaniu kopiowania ustawiono zachowanie "PreserveHierarchy".
-   Podczas kopiowania wielu plików binarnych w działaniu pojedynczej kopii z włączoną weryfikacją spójności danych istnieje możliwość przerwania działania kopiowania lub kontynuowania kopiowania reszty przez włączenie ustawienia odporności na uszkodzenia w celu pominięcia niespójnych plików. 
-   W przypadku kopiowania tabeli w działaniu pojedynczej kopii z włączoną weryfikacją spójności danych działanie kopiowania zakończy się niepowodzeniem, jeśli liczba wierszy odczytanych ze źródła jest różna od liczby wierszy skopiowanych do miejsca docelowego oraz liczby pominiętych wierszy, które były pomijane.


## <a name="configuration"></a>Konfigurowanie
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
validateDataConsistency | Jeśli dla tej właściwości zostanie ustawiona wartość true, podczas kopiowania plików binarnych działanie Copy sprawdzi rozmiar pliku, lastModifiedDate i sumę kontrolną MD5 dla każdego pliku binarnego skopiowanego ze źródła do magazynu docelowego, aby zapewnić spójność danych między magazynem źródłowym a docelowym. Podczas kopiowania danych tabelarycznych działanie Copy sprawdzi całkowitą liczbę wierszy po zakończeniu zadania, aby upewnić się, że łączna liczba wierszy odczytanych ze źródła jest taka sama jak liczba wierszy skopiowanych do miejsca docelowego oraz liczba pominiętych wierszy, które zostały pominięci. Należy pamiętać, że ta opcja będzie miała wpływ na wydajność kopiowania.  | Prawda<br/>False (domyślnie) | Nie
dataInconsistency | Jeden z par klucz-wartość w zbiorze właściwości skipErrorFile, aby określić, czy chcesz pominąć niespójne pliki. <br/> -True: chcesz skopiować resztę, pomijając niespójne pliki.<br/> -False: chcesz przerwać działanie kopiowania po znalezieniu niespójnego pliku.<br/>Należy pamiętać, że ta właściwość jest prawidłowa tylko w przypadku kopiowania plików binarnych i ustawiania validateDataConsistency jako true.  | Prawda<br/>False (domyślnie) | Nie
logSettings | Grupa właściwości, które można określić, aby umożliwić dziennikowi sesji rejestrowanie pominiętych plików. | | Nie
linkedServiceName | Połączona usługa [systemu Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) do przechowywania plików dziennika sesji. | Nazwy `AzureBlobStorage` `AzureBlobFS` połączonych usług lub typów, które odnoszą się do wystąpienia używanego do przechowywania plików dziennika. | Nie
path | Ścieżka do plików dziennika. | Określ ścieżkę, w której mają być przechowywane pliki dziennika. Jeśli nie podasz ścieżki, usługa utworzy dla Ciebie kontener. | Nie

>[!NOTE]
>- Podczas kopiowania plików binarnych z, lub do obiektu blob platformy Azure lub Azure Data Lake Storage Gen2, usługa ADF umożliwia weryfikację sum kontrolnych MD5 poziomu blokowego, wykorzystując [interfejs API obiektów blob platformy Azure](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) i [interfejs API Azure Data Lake Storage Gen2](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers). Jeśli ContentMD5 w plikach istnieje w obiekcie blob platformy Azure lub Azure Data Lake Storage Gen2 jako źródła danych, na AUTOMATYCZNYm przeczytaniu plików zostanie zweryfikowana suma kontrolna MD5 na poziomie pliku. Po skopiowaniu plików do obiektu blob platformy Azure lub Azure Data Lake Storage Gen2 jako miejsce docelowe danych usługa ADF zapisuje ContentMD5 do obiektu blob platformy Azure lub Azure Data Lake Storage Gen2, które mogą być dodatkowo używane przez aplikacje podrzędne do weryfikacji spójności danych.
>- W przypadku kopiowania plików binarnych między dowolnymi magazynami magazynów jest przeprowadzana weryfikacja rozmiaru pliku.

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
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
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
W pliku dziennika powyżej można zobaczyć, że sample1.csv został pominięty, ponieważ nie można było zweryfikować, że jest on spójny między magazynem źródłowym i docelowym. Możesz uzyskać więcej szczegółów na temat tego, dlaczego sample1.csv stanie się niespójna, ponieważ była zmieniana przez inne aplikacje, gdy kopiowanie odbywa się w tym samym czasie. 



## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Odporność na uszkodzenia działania kopiowania](copy-activity-fault-tolerance.md)