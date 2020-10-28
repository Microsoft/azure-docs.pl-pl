---
title: Naprawianie zadania eksportu Azure Import/Export — wersja 1 | Microsoft Docs
description: Dowiedz się, jak naprawić zadanie eksportu, które zostało utworzone i uruchomione za pomocą usługi Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2017
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 35738db5b7bd4a1ac7aaf94e2dc5f1d26a075cdf
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791838"
---
# <a name="repairing-an-export-job"></a>Naprawianie zadania eksportu
Po zakończeniu zadania eksportu można uruchomić narzędzie Microsoft Azure Import/Export lokalnie, aby:  
  
1.  Pobierz wszystkie pliki, których nie można wyeksportować za pomocą usługi Azure Import/Export.  
  
2.  Sprawdź, czy pliki na dysku zostały prawidłowo wyeksportowane.  
  
Aby korzystać z tej funkcji, musisz mieć połączenie z usługą Azure Storage.  
  
Polecenie naprawy zadania importowania to **RepairExport** .

## <a name="repairexport-parameters"></a>Parametry RepairExport

Następujące parametry można określić za pomocą **RepairExport** :  
  
|Parametr|Opis|  
|---------------|-----------------|  
|**/r: <RepairFile\>**|Wymagane. Ścieżka do pliku naprawy, która śledzi postęp naprawy i pozwala na wznowienie przerwanej naprawy. Każdy dysk musi mieć jeden i tylko jeden plik naprawy. Po rozpoczęciu naprawy danego dysku należy przekazać ścieżkę do pliku naprawy, który jeszcze nie istnieje. Aby wznowić przerwaną naprawę, należy przekazać nazwę istniejącego pliku naprawy. Zawsze określaj plik naprawy odpowiadający dyskowi docelowemu.|  
|**/logdir: <LogDirectory\>**|Opcjonalny. Katalog dzienników. Pełne pliki dziennika będą zapisywane w tym katalogu. Jeśli nie określono katalogu dziennika, bieżący katalog będzie używany jako katalog dziennika.|  
|**/d: <TargetDirectory\>**|Wymagane. Katalog, który ma zostać zweryfikowany i naprawiony. Ten katalog jest zazwyczaj katalogiem głównym dysku eksportu, ale może być również sieciowym udziałem plików zawierającym kopię eksportowanych plików.|  
|**/BK: <BitLockerKey\>**|Opcjonalny. Określ klucz funkcji BitLocker, jeśli chcesz, aby narzędzie odblokowało zaszyfrowany plik, w którym są przechowywane eksportowane pliki.|  
|**/SN: <StorageAccountName\>**|Wymagane. Nazwa konta magazynu dla zadania eksportu.|  
|**/SK: <StorageAccountKey\>**|**Wymagane** , jeśli i tylko wtedy, gdy nie określono sygnatury dostępu współdzielonego kontenera. Klucz konta magazynu dla zadania eksportu.|  
|**/CSAS: <ContainerSas\>**|**Wymagane** , jeśli i tylko wtedy, gdy nie określono klucza konta magazynu. Kontener SAS kontenera do uzyskiwania dostępu do obiektów BLOB skojarzonych z zadaniem eksportu.|  
|**/CopyLogFile: <DriveCopyLogFile\>**|Wymagane. Ścieżka do pliku dziennika kopiowania dysku. Plik jest generowany przez usługę Import/Export systemu Windows Azure i można go pobrać z magazynu obiektów BLOB skojarzonego z zadaniem. Plik dziennika kopiowania zawiera informacje dotyczące niezakończonych obiektów blob lub plików, które mają zostać naprawione.|  
|**/ManifestFile: <DriveManifestFile\>**|Opcjonalny. Ścieżka do pliku manifestu dysku eksportu. Ten plik jest generowany przez usługę Import/Export systemu Windows Azure i przechowywany na dysku eksportu. Opcjonalnie w obiekcie BLOB na koncie magazynu skojarzonym z zadaniem.<br /><br /> Zawartość plików na dysku eksportu zostanie zweryfikowana przy użyciu skrótów MD5 zawartych w tym pliku. Wszystkie uszkodzone pliki zostaną pobrane i ponownie umieszczone w katalogach docelowych.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Poprawianie nieudanych eksportów przy użyciu trybu RepairExport  
Aby pobrać pliki, które nie zostały wyeksportowane, można użyć narzędzia Azure Import/Export. Plik dziennika kopiowania będzie zawierać listę plików, które nie zostały wyeksportowane.  
  
Przyczyny niepowodzeń eksportu obejmują następujące możliwości:  
  
-   Uszkodzone dyski  
  
-   Klucz konta magazynu został zmieniony podczas procesu transferu  
  
Aby uruchomić narzędzie w trybie **RepairExport** , należy najpierw podłączyć dysk zawierający wyeksportowane pliki do komputera. Następnie uruchom narzędzie Azure Import/Export, określając ścieżkę do tego dysku przy użyciu `/d` parametru. Należy również określić ścieżkę do pobranego pliku dziennika kopiowania dysku. Poniższy przykład wiersza polecenia uruchamia narzędzie w celu naprawy wszystkich plików, które nie zostały wyeksportowane:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Poniższy przykład to plik dziennika kopiowania, który pokazuje, że eksportowanie jednego bloku obiektu BLOB nie powiodło się:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Plik dziennika kopiowania wskazuje, że wystąpił błąd podczas pobierania przez usługę Windows Azure Import/Export jednego z bloków obiektu BLOB do pliku na dysku eksportu. Pozostałe składniki pliku zostały pobrane pomyślnie, a długość pliku została prawidłowo ustawiona. W takim przypadku narzędzie otworzy plik na dysku, pobierze blok z konta magazynu i zapisze go do zakresu plików rozpoczynając od przesunięcia 65536 o długości 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Weryfikowanie zawartości dysku przy użyciu RepairExport  
Możesz również użyć opcji importowania/eksportowania platformy Azure z opcją **RepairExport** , aby sprawdzić, czy zawartość na dysku jest poprawna. Plik manifestu na każdym dysku eksportu zawiera MD5s dla zawartości dysku.  
  
Usługa Azure Import/Export umożliwia także zapisanie plików manifestu na koncie magazynu podczas procesu eksportowania. Lokalizacja plików manifestu jest dostępna za pośrednictwem operacji [Pobierz zadanie](/rest/api/storageimportexport/jobs) , gdy zadanie zostało ukończone. Aby uzyskać więcej informacji o formacie pliku manifestu dysku, zobacz [plik manifestu usługi Import/Export](/previous-versions/azure/storage/common/storage-import-export-file-format-metadata-and-properties).  
  
Poniższy przykład pokazuje, jak uruchomić narzędzie Azure Import/Export z parametrami **/MANIFESTFILE** i **/CopyLogFile** :  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Poniższy przykład przedstawia plik manifestu:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Po zakończeniu procesu naprawy Narzędzie odczytuje wszystkie pliki, do których odwołuje się plik manifestu, i weryfikuje integralność pliku za pomocą skrótów MD5. W przypadku powyższego manifestu przejdziesz przez następujące składniki.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Każdy składnik niepowodzenia weryfikacji zostanie pobrany przez narzędzie i ponownie zapisany w tym samym pliku na dysku.  
  
## <a name="next-steps"></a>Następne kroki
 
* [Konfigurowanie narzędzia Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import-v1)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania importu](storage-import-export-tool-repairing-an-import-job-v1.md)