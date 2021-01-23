---
title: Naprawianie zadania importowania/eksportowania platformy Azure — Wersja 1 | Microsoft Docs
description: Dowiedz się, jak naprawić zadanie importu, które zostało utworzone i uruchomione za pomocą usługi Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: af6234a81e8f79424cc3b1ec4178a175d9ff0146
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706711"
---
# <a name="repairing-an-import-job"></a>Naprawianie zadania importu
Usługa Microsoft Azure Import/Export może nie móc skopiować niektórych plików lub części pliku do usługi Windows Azure Blob. Przyczyny niepowodzeń mogą być następujące:  
  
-   Uszkodzone pliki  
  
-   Uszkodzone dyski  
  
-   Klucz konta magazynu został zmieniony, gdy plik był transferowany.  
  
Narzędzie Microsoft Azure Import/Export można uruchomić za pomocą plików dziennika kopiowania zadania importu. Narzędzie przekazuje brakujące pliki lub części pliku do konta usługi Microsoft Azure Storage, aby zakończyć zadanie importowania.  
  
## <a name="repairimport-parameters"></a>Parametry RepairImport

Następujące parametry można określić za pomocą **RepairImport**: 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Wymagane.** Ścieżka do pliku naprawy, która śledzi postęp naprawy i pozwala na wznowienie przerwanej naprawy. Każdy dysk musi mieć jeden i tylko jeden plik naprawy. Po rozpoczęciu naprawy danego dysku Przekaż ścieżkę do pliku naprawy, który jeszcze nie istnieje. Aby wznowić przerwaną naprawę, należy przekazać nazwę istniejącego pliku naprawy. Zawsze określaj plik naprawy odpowiadający dyskowi docelowemu.|  
|**/logdir:**<LogDirectory\>|**Obowiązkowe.** Katalog dzienników. Pełne pliki dziennika są zapisywane w tym katalogu. Jeśli nie określono katalogu dziennika, bieżący katalog jest używany jako katalog dziennika.|  
|**/d:**<TargetDirectories\>|**Wymagane.** Jeden lub więcej katalogów rozdzielonych średnikami, które zawierają oryginalne pliki, które zostały zaimportowane. Można również użyć dysku importu, ale nie jest to wymagane, jeśli są dostępne alternatywne lokalizacje plików oryginalnych.|  
|**/BK:**<BitLockerKey\>|**Obowiązkowe.** Określ klucz funkcji BitLocker, jeśli chcesz, aby narzędzie odblokowało zaszyfrowany dysk, na którym są dostępne pliki oryginalne.|  
|**/SN:**<StorageAccountName\>|**Wymagane.** Nazwa konta magazynu dla zadania importu.|  
|**/sk:**<StorageAccountKey\>|**Wymagane** , jeśli i tylko wtedy, gdy nie określono sygnatury dostępu współdzielonego kontenera. Klucz konta magazynu dla zadania importu.|  
|**/CSAS:**<ContainerSas\>|**Wymagane** , jeśli i tylko wtedy, gdy nie określono klucza konta magazynu. Kontener SAS kontenera do uzyskiwania dostępu do obiektów BLOB skojarzonych z zadaniem importu.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Wymagane.** Ścieżka do pliku dziennika kopiowania dysku (pełny dziennik lub dziennik błędów). Plik jest generowany przez usługę Import/Export systemu Windows Azure i można go pobrać z magazynu obiektów BLOB skojarzonego z zadaniem. Plik dziennika kopiowania zawiera informacje dotyczące niezakończonych obiektów blob lub plików, które mają zostać naprawione.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Obowiązkowe.** Ścieżka do pliku tekstowego, który służy do rozpoznawania niejasności, jeśli istnieje wiele plików o tej samej nazwie, która została zaimportowana w ramach tego samego zadania. Gdy narzędzie jest uruchamiane po raz pierwszy, może wypełnić ten plik wszystkimi niejednoznacznymi nazwami. Późniejsze uruchomienia narzędzia używają tego pliku do rozwiązania niejasności.|  
  
## <a name="using-the-repairimport-command"></a>Przy użyciu polecenia RepairImport  
Aby naprawić dane importu przez przesyłanie strumieniowe danych za pośrednictwem sieci, należy określić katalogi zawierające oryginalne pliki, które zostały zaimportowane przy użyciu `/d` parametru. Określ również plik dziennika kopiowania pobrany z konta magazynu. Typowy wiersz polecenia służący do naprawy zadania importu z błędami częściowymi wygląda następująco:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
W poniższym przykładzie pliku dziennika kopiowania 1 64-K część pliku została uszkodzona na dysku, który został dostarczony dla zadania importu. Ponieważ ten błąd jest jedynym wskazanym, pozostałe obiekty blob w zadaniu zostały pomyślnie zaimportowane.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Gdy ten dziennik kopiowania jest przesyłany do narzędzia Azure Import/Export, Narzędzie próbuje zakończyć importowanie dla tego pliku przez skopiowanie brakującej zawartości w sieci. W powyższym przykładzie narzędzie szuka oryginalnego pliku `\animals\koala.jpg` w dwóch katalogach `C:\Users\bob\Pictures` i `X:\BobBackup\photos` . Jeśli plik `C:\Users\bob\Pictures\animals\koala.jpg` istnieje, narzędzie Azure Import/Export kopiuje brakujący zakres danych do odpowiedniego obiektu BLOB `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg` .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Rozwiązywanie konfliktów podczas korzystania z RepairImport  
W niektórych sytuacjach narzędzie może nie być w stanie znaleźć lub otworzyć wymaganego pliku z jednego z następujących powodów: nie można odnaleźć pliku, plik nie jest dostępny, nazwa pliku jest niejednoznaczna lub zawartość pliku nie jest już poprawna.  
  
Może wystąpić niejednoznaczny błąd, jeśli narzędzie próbuje zlokalizować `\animals\koala.jpg` i istnieje plik o tej nazwie w ramach obu `C:\Users\bob\pictures` i `X:\BobBackup\photos` . Oznacza to, że zarówno, `C:\Users\bob\pictures\animals\koala.jpg` jak i `X:\BobBackup\photos\animals\koala.jpg` istnieją na dyskach zadań importowania.  
  
`/PathMapFile`Opcja pozwala rozwiązać te błędy. Możesz określić nazwę pliku, który zawiera listę plików, które narzędzie nie może poprawnie zidentyfikować. Poniższy przykład wiersza polecenia wypełnia `9WM35C2V_pathmap.txt` :  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Następnie narzędzie zapisze problematyczne ścieżki plików do `9WM35C2V_pathmap.txt` , po jednym w każdym wierszu. Na przykład po uruchomieniu polecenia plik może zawierać następujące wpisy:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Dla każdego pliku na liście należy spróbować zlokalizować i otworzyć plik, aby upewnić się, że jest on dostępny dla narzędzia. Jeśli chcesz powiadomić narzędzie jawnie, gdzie znaleźć plik, zmodyfikuj plik mapy ścieżki i Dodaj ścieżkę do każdego pliku w tym samym wierszu, oddzielone znakiem tabulacji:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Po udostępnieniu niezbędnych plików dla narzędzia lub zaktualizowaniu pliku mapy ścieżki możesz ponownie uruchomić narzędzie, aby zakończyć proces importowania.  
  
## <a name="next-steps"></a>Następne kroki
 
* [Konfigurowanie narzędzia Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania eksportu](./storage-import-export-tool-repairing-an-export-job-v1.md)