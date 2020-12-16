---
title: Rozwiązywanie problemów z przekazywaniem danych przy użyciu dzienników
titleSuffix: Azure Data Box Disk
description: Opisuje sposób korzystania z dzienników i rozwiązywania problemów występujących podczas przekazywania danych do Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 4b53cf607bdf60c785c7324d9ede526a0983b7e6
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605279"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Omówienie dzienników służących do rozwiązywania problemów z przekazywaniem danych w Azure Data Box Disk

Ten artykuł ma zastosowanie do Microsoft Azure Data Box Disk i opisuje problemy, które są wyświetlane podczas przekazywania danych do platformy Azure.

## <a name="about-upload-logs"></a>Informacje o dziennikach przekazywania

Gdy dane są przekazywane do platformy Azure w centrum danych, `_error.xml` a `_verbose.xml` pliki są generowane dla każdego konta magazynu. Te dzienniki są przekazywane do tego samego konta magazynu, które zostało użyte do przekazania danych. 

Oba dzienniki są w tym samym formacie i zawierają opisy XML zdarzeń, które wystąpiły podczas kopiowania danych z dysku na konto usługi Azure Storage.

Pełny dziennik zawiera pełne informacje o stanie operacji kopiowania dla każdego obiektu BLOB lub pliku, podczas gdy dziennik błędów zawiera tylko te informacje dla obiektów blob lub plików, które napotkały błędy podczas przekazywania.

Dziennik błędów ma tę samą strukturę co dziennik pełny, ale filtruje pomyślne operacje.

## <a name="download-logs"></a>Pobieranie dzienników

Aby zlokalizować dzienniki przekazywania, wykonaj następujące czynności.

1. Jeśli wystąpią błędy podczas przekazywania danych na platformę Azure, w portalu zostanie wyświetlona ścieżka do folderu, w którym znajdują się dzienniki diagnostyki.

    ![Link do dzienników w portalu](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Przejdź do **waies**.

    ![Błędy i pełne dzienniki](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

W każdym przypadku wyświetlane są dzienniki błędów i pełne dzienniki. Wybierz każdy dziennik i Pobierz kopię lokalną.

## <a name="sample-upload-logs"></a>Przykładowe dzienniki przekazywania

`_verbose.xml`Poniżej pokazano przykład. W takim przypadku zamówienie zostało zakończone pomyślnie, bez błędów.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

W tej samej kolejności przykład `_error.xml` poniżej jest przedstawiony poniżej.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Poniżej przedstawiono przykład, w `_error.xml` którym zamówienie zostało zakończone z błędami. 

Plik błędu w tym przypadku zawiera `Summary` sekcję i inną sekcję, która zawiera wszystkie błędy na poziomie pliku. 

`Summary`Zawiera `ValidationErrors` i `CopyErrors` . W takim przypadku przekazano 8 plików lub folderów na platformę Azure i nie wystąpiły błędy sprawdzania poprawności. Po skopiowaniu danych na konto usługi Azure Storage pomyślnie przekazano 5 plików lub folderów. Pozostałe 3 pliki lub foldery zostały zmienione zgodnie z konwencjami nazewnictwa kontenerów platformy Azure, a następnie pomyślnie przekazane do platformy Azure.

Na poziomie pliku znajdują się `BlobStatus` Informacje o akcjach podjętych w celu przekazania obiektów BLOB. W takim przypadku zmieniono nazwy trzech kontenerów, ponieważ foldery, do których skopiowano dane, nie są zgodne z konwencjami nazewnictwa platformy Azure dla kontenerów. W przypadku obiektów BLOB przekazanych w tych kontenerach Nowa nazwa kontenera, ścieżka obiektu BLOB na platformie Azure, oryginalna nieprawidłowa ścieżka do pliku i rozmiar obiektu BLOB są uwzględniane.
  
```xml
 <?xml version="1.0" encoding="utf-8"?>
  <DriveLog Version="2018-10-01">
    <DriveId>18041C582D7E</DriveId>
    <Summary>
     <!--Summary for validation and data copy to Azure -->
      <ValidationErrors>
        <None Count="8" />
      </ValidationErrors>
      <CopyErrors>
        <Completed Count="5" Description="No errors encountered" />
        <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
      </CopyErrors>
    </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
      <SizeInBytes>71528448</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Status>CompletedWithErrors</Status>
  </DriveLog>
```

## <a name="data-upload-errors"></a>Błędy przekazywania danych

Błędy generowane podczas przekazywania danych na platformę Azure zostały podsumowane w poniższej tabeli.

| Kod błędu | Opis                   |
|-------------|------------------------------|
|`None` |  Ukończono pomyślnie.           |
|`Renamed` | Pomyślnie zmieniono nazwę obiektu BLOB.   |
|`CompletedWithErrors` | Przekazywanie zostało zakończone z błędami. Szczegóły plików z błędami są zawarte w pliku dziennika.  |
|`Corrupted`|Kod CRC obliczany podczas pozyskiwania danych nie jest zgodny z CRC obliczanym podczas przekazywania.  |  
|`StorageRequestFailed` | Żądanie usługi Azure Storage nie powiodło się.   |     
|`LeasePresent` | Ten element jest dzierżawiony i jest używany przez innego użytkownika. |
|`StorageRequestForbidden` |Nie można przekazać z powodu problemów z uwierzytelnianiem. |
|`ManagedDiskCreationTerminalFailure` | Nie można przekazać jako dysków zarządzanych. Pliki są dostępne na koncie magazynu przemieszczania jako stronicowe obiekty blob. Można ręcznie konwertować stronicowe obiekty blob na dyski zarządzane.  |
|`DiskConversionNotStartedTierInfoMissing` | Ponieważ plik VHD został skopiowany poza foldery warstw pretworzonych, nie został utworzony dysk zarządzany. Plik zostanie przekazany jako obiekt BLOB strony do konta magazynu tymczasowego określonego podczas tworzenia kolejności. Można przekonwertować ją ręcznie na dysk zarządzany.|
|`InvalidWorkitem` | Nie można przekazać danych, ponieważ nie są zgodne z konwencjami nazewnictwa i ograniczeniami platformy Azure.|
|`InvalidPageBlobUploadAsBlockBlob` | Przekazane jako blokowe obiekty blob w kontenerze z prefiksem `databoxdisk-invalid-pb-` .|
|`InvalidAzureFileUploadAsBlockBlob` | Przekazane jako blokowe obiekty blob w kontenerze z prefiksem `databoxdisk-invalid-af` -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Przekazane jako blokowe obiekty blob w kontenerze z prefiksem `databoxdisk-invalid-md` -.|
|`InvalidManagedDiskUploadAsPageBlob` |Przekazane jako stronicowe obiekty blob w kontenerze z prefiksem `databoxdisk-invalid-md-` . |
|`MovedToOverflowShare` |Przekazano pliki do nowego udziału, ponieważ rozmiar oryginalnego udziału przekracza maksymalny limit rozmiaru platformy Azure. Nazwa nowego udziału plików ma sufiks `-2` .   |
|`MovedToDefaultAzureShare` |Przekazano pliki, które nie zostały częścią żadnego folderu do udziału domyślnego. Nazwa udziału zaczyna się od `databox-` . |
|`ContainerRenamed` |Kontener dla tych plików nie jest zgodny z konwencjami nazewnictwa platformy Azure i ma nazwę. Nowa nazwa rozpoczyna się od `databox-` i jest sufiksem skrótu SHA1 oryginalnej nazwy |
|`ShareRenamed` |Udział tych plików nie jest zgodny z konwencjami nazewnictwa platformy Azure i ma nazwę. Nowa nazwa rozpoczyna się od `databox-` i jest sufiksem skrótu SHA1 oryginalnej nazwy. |
|`BlobRenamed` |Te pliki nie są zgodne z konwencjami nazewnictwa platformy Azure i zmieniono ich nazwy. Sprawdź `BlobPath` pole pod kątem nowej nazwy. |
|`FileRenamed` |Te pliki nie są zgodne z konwencjami nazewnictwa platformy Azure i zmieniono ich nazwy. Sprawdź `FileStoragePath` pole pod kątem nowej nazwy. |
|`DiskRenamed` |Te pliki nie są zgodne z konwencjami nazewnictwa platformy Azure i zmieniono ich nazwy. Sprawdź `BlobPath` pole pod kątem nowej nazwy. |


## <a name="next-steps"></a>Następne kroki

- [Otwórz bilet pomocy technicznej, aby uzyskać Data Box Disk problemy](data-box-disk-contact-microsoft-support.md).
