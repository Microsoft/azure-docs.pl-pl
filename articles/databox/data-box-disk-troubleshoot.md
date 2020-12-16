---
title: Rozwiązywanie problemów z usługą Azure Data Box Disk | Microsoft Docs
description: Informacje o sposobach korzystania z dzienników w celu rozwiązywania problemów z walidacją, które mogą wystąpić podczas wdrażania Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: fa8a6643f1b7bd60fbf6e5950234e0381666177e
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605228"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Korzystanie z dzienników w celu rozwiązywania problemów ze sprawdzaniem poprawności w Azure Data Box Disk

Ten artykuł ma zastosowanie do Data Box Disk Microsoft Azure. W tym artykule opisano sposób korzystania z dzienników w celu rozwiązywania problemów z walidacją, które mogą zostać wyświetlone podczas wdrażania tego rozwiązania.

## <a name="validation-tool-log-files"></a>Pliki dziennika narzędzia walidacji

Po sprawdzeniu poprawności danych na dyskach za pomocą [Narzędzia sprawdzania poprawności](data-box-disk-deploy-copy-data.md#validate-data)zostanie wygenerowane *error.xml* , aby rejestrować błędy. Plik dziennika znajduje się w  `Drive:\DataBoxDiskImport\logs` folderze dysku. Po uruchomieniu walidacji jest dostępny link do dziennika błędów.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

W przypadku uruchomienia wielu sesji w celu sprawdzenia poprawności zostanie wygenerowany jeden dziennik błędów dla każdej sesji.

- Poniżej znajduje się przykładowy dziennik błędów, gdy dane załadowane do `PageBlob` folderu nie są 512-bajtowe wyrównane. Wszystkie dane przekazane do PageBlob muszą być wyrównane do 512-bajtowe, na przykład dysk VHD lub VHDX. Błędy w tym pliku znajdują się w tematach `<Errors>` i `<Warnings>` .

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Poniżej znajduje się przykładowy dziennik błędów, gdy nazwa kontenera jest nieprawidłowa. Folder utworzony w folderze `BlockBlob` , `PageBlob` czyli lub `AzureFile` foldery na dysku staną się kontenerami na koncie usługi Azure Storage. Nazwa kontenera musi być zgodna z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ErrorLog Version="2018-10-01">
        <SessionId>bbsession</SessionId>
        <ItemType>BlockBlob</ItemType>
        <SourceDirectory>E:\BlockBlob</SourceDirectory>
        <Errors>
        <Error Code="InvalidShareContainerFormat">
            <List>
            <Container Name="Azu-reFile" />
            <Container Name="bbcont ainer1" />
            </List>
            <Count>2</Count>
        </Error>
        </Errors>
        <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Błędy narzędzi walidacji

Błędy zawarte w *error.xml* z odpowiednimi zalecanymi akcjami zostały podsumowane w poniższej tabeli.

| Kod błędu| Opis                       | Zalecane akcje               |
|------------|--------------------------|-----------------------------------|
| `None` | Pomyślnie zweryfikowano dane. | Nie jest wymagana żadna akcja. |
| `InvalidXmlCharsInPath` |Nie można utworzyć pliku manifestu, ponieważ ścieżka pliku zawiera nieprawidłowe znaki. | Usuń te znaki, aby wykonać operację.  |
| `OpenFileForReadFailed`| Nie można przetworzyć pliku. Może to być spowodowane problemem z dostępem lub uszkodzeniem systemu plików.|Nie można odczytać pliku z powodu błędu. Szczegóły błędu znajdują się w wyjątku. |
| `Not512Aligned` | Ten plik ma nieprawidłowy format dla folderu PageBlob.| Przekaż tylko dane z 512 bajtami wyrównanymi do `PageBlob` folderu. Usuń plik z folderu PageBlob lub przenieś go do folderu BlockBlob. Spróbuj ponownie wykonać weryfikację.|
| `InvalidBlobPath` | Ścieżka pliku nie jest zmapowana do prawidłowej ścieżki obiektu BLOB w chmurze zgodnie z konwencjami nazewnictwa obiektów blob platformy Azure.|Postępuj zgodnie z wytycznymi nazewnictwa platformy Azure, aby zmienić nazwę ścieżki pliku. |
| `EnumerationError` | Nie można wyliczyć pliku do walidacji. |Może istnieć wiele przyczyn tego błędu. Najbardziej prawdopodobną przyczyną jest dostęp do pliku. |
| `ShareSizeExceeded` | Ten plik spowodował, że rozmiar udziału plików platformy Azure przekracza limit platformy Azure wynoszący 5 TB.|Zmniejsz rozmiar danych w udziale, aby była zgodna z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Spróbuj ponownie wykonać weryfikację. |
| `AzureFileSizeExceeded` | Rozmiar pliku przekracza limity rozmiaru plików platformy Azure.| Zmniejsz rozmiar pliku lub danych, tak aby był zgodny z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Spróbuj ponownie wykonać weryfikację.|
| `BlockBlobSizeExceeded` | Rozmiar pliku przekracza limity rozmiaru obiektów BLOB bloku platformy Azure. | Zmniejsz rozmiar pliku lub danych, tak aby był zgodny z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Spróbuj ponownie wykonać weryfikację. |
| `ManagedDiskSizeExceeded` | Rozmiar pliku przekracza limity rozmiaru dysku zarządzanego przez platformę Azure. | Zmniejsz rozmiar pliku lub danych, tak aby był zgodny z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Spróbuj ponownie wykonać weryfikację. |
| `PageBlobSizeExceeded` | Rozmiar pliku przekracza limity rozmiaru dysku zarządzanego przez platformę Azure. | Zmniejsz rozmiar pliku lub danych, tak aby był zgodny z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Spróbuj ponownie wykonać weryfikację. |
| `InvalidShareContainerFormat`  |Nazwy katalogów nie są zgodne z konwencjami nazewnictwa platformy Azure dla kontenerów lub udziałów.         |Pierwszy folder utworzony we wstępnie istniejących folderach na dysku jest kontenerem na koncie magazynu. Ten udział lub nazwa kontenera nie jest zgodna z konwencjami nazewnictwa platformy Azure. Zmień nazwę pliku tak, aby był zgodny z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Spróbuj ponownie wykonać weryfikację.   |
| `InvalidBlobNameFormat` | Ścieżka pliku nie jest zmapowana do prawidłowej ścieżki obiektu BLOB w chmurze zgodnie z konwencjami nazewnictwa obiektów blob platformy Azure.|Zmień nazwę pliku tak, aby był zgodny z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Spróbuj ponownie wykonać weryfikację. |
| `InvalidFileNameFormat` | Ścieżka pliku nie jest zmapowana do prawidłowej ścieżki pliku w chmurze zgodnie z konwencjami nazewnictwa plików platformy Azure. |Zmień nazwę pliku tak, aby był zgodny z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Spróbuj ponownie wykonać weryfikację. |
| `InvalidDiskNameFormat` | Ścieżka pliku nie jest zmapowana do prawidłowej nazwy dysku w chmurze zgodnie z konwencjami nazewnictwa dysków zarządzanych przez platformę Azure. |Zmień nazwę pliku tak, aby był zgodny z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Spróbuj ponownie wykonać weryfikację.       |
| `NotPartOfFileShare` | Ścieżka przekazywania plików jest nieprawidłowa. Przekaż pliki do folderu w Azure Files.   | Usuń pliki z błędami i przekaż te pliki do folderu pretworzonego. Spróbuj ponownie wykonać weryfikację. |
| `NonVhdFileNotSupportedForManagedDisk` | Nie można przekazać pliku bez dysku VHD jako dysku zarządzanego. |Usuń pliki inne niż VHD z `ManagedDisk` folderu, ponieważ nie są one obsługiwane lub przenieś te pliki do `PageBlob` folderu. Spróbuj ponownie wykonać weryfikację. |


## <a name="next-steps"></a>Następne kroki

- Rozwiązywanie problemów z [przekazywaniem danych](data-box-disk-troubleshoot-upload.md).
