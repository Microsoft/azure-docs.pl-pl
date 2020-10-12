---
title: Rozwiązywanie problemów dotyczących Azure Data Box, Azure Data Box Heavy
description: Opisuje sposób rozwiązywania problemów występujących w Azure Data Box i Azure Data Box Heavy podczas kopiowania danych na te urządzenia.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 2a40e908677a173862ad715f7024865ff728d0b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90053457"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Rozwiązywanie problemów związanych z Azure Data Box i Azure Data Box Heavy

Ten artykuł zawiera szczegółowe informacje dotyczące rozwiązywania problemów, które mogą wystąpić podczas korzystania z Azure Data Box lub Azure Data Box Heavy w przypadku zamówień importu. Artykuł zawiera listę możliwych błędów, które pojawiają się, gdy dane są kopiowane do urządzenie Data Box lub gdy dane są przekazywane z urządzenie Data Box w celu zaimportowania.

Informacje przedstawione w tym artykule nie dotyczą zamówień eksportu utworzonych dla urządzenie Data Box.

## <a name="error-classes"></a>Klasy błędów

Błędy w urządzenie Data Box i Data Box Heavy zostały podsumowane w następujący sposób:

| Kategoria błędu *        | Opis        | Zalecana akcja    |
|----------------------------------------------|---------|--------------------------------------|
| Nazwy kontenerów lub udziałów | Nazwy kontenerów lub udziałów nie są zgodne z regułami nazewnictwa platformy Azure.  |Pobierz listy błędów. <br> Zmień nazwę kontenerów lub udziałów. [Dowiedz się więcej](#container-or-share-name-errors).  |
| Limit rozmiaru kontenera lub udziału | Całkowita ilość danych w kontenerach lub udziałach przekracza limit platformy Azure.   |Pobierz listy błędów. <br> Zmniejsz ogólne dane w kontenerze lub udziale. [Dowiedz się więcej](#container-or-share-size-limit-errors).|
| Limit rozmiaru obiektu lub pliku | Obiekt lub pliki w kontenerach lub udziałach przekraczają limit platformy Azure.|Pobierz listy błędów. <br> Zmniejsz rozmiar pliku w kontenerze lub udziale. [Dowiedz się więcej](#object-or-file-size-limit-errors). |    
| Typ danych lub plików | Format danych lub typ pliku nie są obsługiwane. |Pobierz listy błędów. <br> W przypadku stronicowych obiektów blob lub dysków zarządzanych upewnij się, że dane są 512-bajtowe wyrównane i skopiowane do wstępnie utworzonych folderów. [Dowiedz się więcej](#data-or-file-type-errors). |
| Niekrytyczne obiekty blob lub błędy plików  | Nazwy obiektów blob lub plików nie są zgodne z regułami nazewnictwa platformy Azure lub typ pliku nie jest obsługiwany. | Te obiekty blob lub pliki nie mogą być kopiowane lub można zmienić nazwy. [Dowiedz się, jak naprawić te błędy](#non-critical-blob-or-file-errors). |

\* Pierwsze cztery kategorie błędów są błędami krytycznymi i muszą zostać naprawione przed rozpoczęciem przygotowywania do wysłania.


## <a name="container-or-share-name-errors"></a>Błędy nazwy kontenera lub udziału

Są to błędy związane z nazwami kontenerów i udziałów.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Opis błędu:** Nazwa kontenera lub udziału musi zawierać od 3 do 63 znaków. 

**Sugerowane rozwiązanie:** Folder w ramach udziału urządzenie Data Box lub Data Box Heavy (SMB/NFS), do którego skopiowane dane, jest kontenerem platformy Azure na koncie magazynu. 

- Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web na urządzeniu Pobierz i Przejrzyj pliki błędów, aby zidentyfikować nazwy folderów z problemami.
- Zmień nazwę folderu w udziale urządzenie Data Box lub Data Box Heavy, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą zawierać tylko litery, cyfry i łączniki.
    - Nazwy nie mogą rozpoczynać się ani kończyć znakiem łączników.
    - Nazwy nie mogą mieć kolejnych łączników.
    - Przykłady prawidłowych nazw: `my-folder-1` , `my-really-extra-long-folder-111`
    - Przykłady nieprawidłowych nazw: `my-folder_1` , `my` ,, `--myfolder` `myfolder--` , `myfolder!`

    Aby uzyskać więcej informacji, zobacz Konwencje nazewnictwa platformy Azure dla [nazw kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazw udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Opis błędu:** Nazwa kontenera lub udziału musi zawierać tylko litery, cyfry lub łączniki.

**Sugerowane rozwiązanie:** Folder w ramach udziału urządzenie Data Box lub Data Box Heavy (SMB/NFS), do którego skopiowane dane, jest kontenerem platformy Azure na koncie magazynu. 

- Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web na urządzeniu Pobierz i Przejrzyj pliki błędów, aby zidentyfikować nazwy folderów z problemami.
- Zmień nazwę folderu w udziale urządzenie Data Box lub Data Box Heavy, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą zawierać tylko litery, cyfry i łączniki.
    - Nazwy nie mogą rozpoczynać się ani kończyć znakiem łączników.
    - Nazwy nie mogą mieć kolejnych łączników.
    - Przykłady prawidłowych nazw: `my-folder-1` , `my-really-extra-long-folder-111`
    - Przykłady nieprawidłowych nazw: `my-folder_1` , `my` ,, `--myfolder` `myfolder--` , `myfolder!`

    Aby uzyskać więcej informacji, zobacz Konwencje nazewnictwa platformy Azure dla [nazw kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazw udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Opis błędu:** Nazwy kontenerów i nazw udziałów nie mogą rozpoczynać się ani kończyć łącznikiem i nie mogą mieć kolejnych łączników.

**Sugerowane rozwiązanie:** Folder w ramach udziału urządzenie Data Box lub Data Box Heavy (SMB/NFS), do którego skopiowane dane, jest kontenerem platformy Azure na koncie magazynu. 

- Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web na urządzeniu Pobierz i Przejrzyj pliki błędów, aby zidentyfikować nazwy folderów z problemami.
- Zmień nazwę folderu w udziale urządzenie Data Box lub Data Box Heavy, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą zawierać tylko litery, cyfry i łączniki.
    - Nazwy nie mogą rozpoczynać się ani kończyć znakiem łączników.
    - Nazwy nie mogą mieć kolejnych łączników.
    - Przykłady prawidłowych nazw: `my-folder-1` , `my-really-extra-long-folder-111`
    - Przykłady nieprawidłowych nazw: `my-folder_1` , `my` ,, `--myfolder` `myfolder--` , `myfolder!`

    Aby uzyskać więcej informacji, zobacz Konwencje nazewnictwa platformy Azure dla [nazw kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazw udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**Opis błędu**: Nazwa katalogu lub kontenera zawiera niedozwolone znaki.

**Sugerowane rozwiązanie**: Nazwa katalogu lub kontenerów, które zostały skopiowane, zawiera nieobsługiwane znaki.

- Na stronie łączenie i kopiowanie w lokalnym interfejsie użytkownika sieci Web Pobierz i Przejrzyj pliki błędów w celu zidentyfikowania nazw folderów zawierających problemy. 
- Zmień nazwę katalogu lub kontenerów, aby upewnić się, że są one zgodne z konwencjami nazewnictwa platformy Azure.

Aby uzyskać więcej informacji, zobacz Konwencje nazewnictwa platformy Azure dla [katalogów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)   i [kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names).

## <a name="container-or-share-size-limit-errors"></a>Błędy limitu rozmiaru kontenera lub udziału

Są to błędy związane z danymi przekraczającymi rozmiar danych dozwolonych w kontenerze lub udziale.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Opis błędu:** Udział plików platformy Azure ogranicza udział do 5 TiB danych, a na koncie magazynu nie są włączone duże udziały plików. Ten limit został przekroczony dla niektórych udziałów.

**Sugerowane rozwiązanie:** Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web Pobierz i Przejrzyj pliki błędów.

- Zidentyfikuj foldery, które mają ten problem z dzienników błędów i upewnij się, że pliki w tym folderze znajdują się poniżej 5 TiB.
- Limit 5 TiB nie ma zastosowania do konta magazynu, które zezwala na duże udziały plików. Jednak podczas umieszczania zamówienia trzeba mieć skonfigurowane duże udziały plików. 
  - Skontaktuj się z firmą [Pomoc techniczna firmy Microsoft](data-box-disk-contact-microsoft-support.md) i zażądaj nowej etykiety wysyłkowej.
  - [Włącz duże udziały plików na koncie magazynu.](../storage/files/storage-files-how-to-create-large-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [Rozwiń udziały plików na koncie magazynu](../storage/files/storage-files-how-to-create-large-file-share.md#expand-existing-file-shares) i ustaw limit przydziału na 100 TIB.
  
  
## <a name="object-or-file-size-limit-errors"></a>Błędy limitu rozmiaru obiektu lub pliku

Są to błędy związane z danymi przekraczającymi maksymalny rozmiar obiektu lub plik, który jest dozwolony na platformie Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Opis błędu:** Rozmiar pliku przekracza maksymalny rozmiar pliku do przekazania.

**Sugerowane rozwiązanie:** Rozmiar obiektu BLOB lub pliku przekracza maksymalny dozwolony limit przekazywania.

- Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web Pobierz i Przejrzyj pliki błędów.
- Upewnij się, że rozmiary obiektów blob i plików nie przekraczają limitów rozmiaru obiektu platformy Azure.

## <a name="data-or-file-type-errors"></a>Błędy danych lub typów plików

Są to błędy związane z nieobsługiwanym typem pliku lub typem danych znalezionym w kontenerze lub udziale. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Opis błędu:** Obiekt BLOB lub plik jest niepoprawnie wyrównany.

**Sugerowane rozwiązanie:** Udział obiektu BLOB strony na urządzenie Data Box lub Data Box Heavy obsługuje tylko pliki o 512 bajtach (na przykład VHD/VHDX). Wszystkie dane skopiowane do udziału obiektu BLOB stronicowania są przekazywane do platformy Azure jako stronicowe obiekty blob.

Usuń wszystkie dane spoza dysku VHD/VHDX z udziału stronicowego obiektu BLOB. Możesz użyć udziałów do blokowych obiektów blob lub plików platformy Azure dla danych ogólnych.

Aby uzyskać więcej informacji, zobacz [Omówienie stronicowych obiektów BLOB](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Opis błędu:** Nieobsługiwany typ pliku jest obecny w udziale dysku zarządzanego. Dozwolone są tylko stałe wirtualne dyski twarde.

**Sugerowane rozwiązanie:**

- Upewnij się, że do tworzenia dysków zarządzanych są przekazywane tylko stałe wirtualne dyski twarde.
- Pliki VHDX lub **dynamiczne** i **różnicowe** dyski VHD nie są obsługiwane.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Opis błędu:** Katalog nie jest dozwolony w żadnym z istniejących wcześniej folderów dla dysków zarządzanych. W tych folderach można używać tylko stałych dysków VHD.

**Sugerowane rozwiązanie:** W przypadku dysków zarządzanych w poszczególnych udziałach są tworzone następujące trzy foldery, które odnoszą się do kontenerów na koncie magazynu: SSD w warstwie Premium, HDD w warstwie Standardowa i SSD w warstwie Standardowa. Foldery te odpowiadają warstwie wydajności dysku zarządzanego.

- Upewnij się, że skopiujesz stronę danych obiektu BLOB (VHD) do jednego z tych istniejących folderów.
- Folder lub katalog nie jest dozwolony w tych istniejących folderach. Usuń wszystkie foldery utworzone wewnątrz istniejących folderów.

Aby uzyskać więcej informacji, zobacz [Kopiuj do dysków zarządzanych](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Opis błędu:** Linki symboliczne nie są dozwolone w systemie Linux. 

**Sugerowane rozwiązanie:** Linki symboliczne są zazwyczaj łączami, potokami i innymi plikami. Usuń linki lub Rozwiąż linki i skopiuj dane.


## <a name="non-critical-blob-or-file-errors"></a>Niekrytyczne obiekty blob lub błędy plików

Wszystkie Błędy niekrytyczne związane z nazwami obiektów blob, plików lub kontenerów, które są widoczne podczas kopiowania danych, zostały podsumowane w poniższej sekcji. Jeśli te błędy są obecne, nazwy zostaną zmodyfikowane, aby były zgodne z konwencjami nazewnictwa platformy Azure. Odpowiedni stan zamówienia przekazywania danych zostanie **ukończony z ostrzeżeniami**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Opis błędu:** Nazwy obiektów blob lub plików zawierają nieobsługiwane znaki kontrolne.

**Sugerowane rozwiązanie:** Obiekty blob lub pliki, które zostały skopiowane, zawierają nazwy z nieobsługiwanymi znakami.

Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web Pobierz i Przejrzyj pliki błędów.
Usuń pliki lub zmień ich nazwy, aby usunąć nieobsługiwane znaki.

Aby uzyskać więcej informacji, zobacz Konwencje nazewnictwa platformy Azure dla [nazw obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazw plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Opis błędu:** Nazwa obiektu BLOB lub pliku zawiera niedozwolone znaki.

**Sugerowane rozwiązanie:** Obiekty blob lub pliki, które zostały skopiowane, zawierają nazwy z nieobsługiwanymi znakami.

Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web Pobierz i Przejrzyj pliki błędów.
Usuń pliki lub zmień ich nazwy, aby usunąć nieobsługiwane znaki.

Aby uzyskać więcej informacji, zobacz Konwencje nazewnictwa platformy Azure dla [nazw obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazw plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Opis błędu:** Nazwy obiektów blob lub plików kończą się nieprawidłowymi znakami.

**Sugerowane rozwiązanie:** Obiekty blob lub pliki, które zostały skopiowane, zawierają nazwy z nieobsługiwanymi znakami.

Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web Pobierz i Przejrzyj pliki błędów.
Usuń pliki lub zmień ich nazwy, aby usunąć nieobsługiwane znaki.

Aby uzyskać więcej informacji, zobacz Konwencje nazewnictwa platformy Azure dla [nazw obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazw plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Opis błędu:** Obiekt BLOB lub nazwa pliku zawiera zbyt wiele segmentów ścieżki.

**Sugerowane rozwiązanie:** Obiekty blob lub pliki, które zostały skopiowane, przekraczają maksymalną liczbę segmentów ścieżki. Segment ścieżki jest ciągiem między kolejnymi znakami ograniczającymi, na przykład ukośnikiem/.

- Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web Pobierz i Przejrzyj pliki błędów.
- Upewnij się, że [nazwy obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazwy plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) są zgodne z konwencjami nazewnictwa platformy Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Opis błędu:** Obiekt BLOB lub nazwa pliku jest za długa.

**Sugerowane rozwiązanie:** Rozmiar obiektu BLOB lub nazwy pliku przekracza maksymalną długość.

- Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web Pobierz i Przejrzyj pliki błędów.
- Nazwa obiektu BLOB nie może przekraczać 1 024 znaków.
- Usuń lub Zmień nazwę obiektu BLOB lub plików tak, aby nazwy nie przekraczały 1024 znaków.

Aby uzyskać więcej informacji, zobacz Konwencje nazewnictwa platformy Azure dla nazw obiektów blob i nazw plików.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Opis błędu:** Jeden z segmentów obiektu BLOB lub nazwy pliku jest zbyt długi.

**Sugerowane rozwiązanie:** Jeden z segmentów ścieżki w obiekcie blob lub nazwie pliku przekracza maksymalną liczbę znaków. Segment ścieżki jest ciągiem między kolejnymi znakami ograniczającymi, na przykład ukośnikiem/.

- Na stronie **łączenie i kopiowanie** w lokalnym interfejsie użytkownika sieci Web Pobierz i Przejrzyj pliki błędów.
- Upewnij się, że [nazwy obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazwy plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) są zgodne z konwencjami nazewnictwa platformy Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Opis błędu:** Określono nieprawidłową nazwę kontenera dla udziałów dysków zarządzanych.

**Sugerowane rozwiązanie:** W przypadku dysków zarządzanych w poszczególnych udziałach są tworzone następujące foldery, które odpowiadają kontenerom na koncie magazynu: SSD w warstwie Premium, HDD w warstwie Standardowa i SSD w warstwie Standardowa. Foldery te odpowiadają warstwie wydajności dysku zarządzanego.

- Upewnij się, że skopiujesz stronę danych obiektu BLOB (VHD) do jednego z tych istniejących folderów. Tylko dane z tych istniejących kontenerów są przekazywane do platformy Azure.
- Wszystkie inne foldery, które są tworzone na tym samym poziomie co SSD w warstwie Premium, HDD w warstwie Standardowa i SSD w warstwie Standardowa, nie odpowiadają prawidłowym warstwom wydajności i nie mogą być używane.
- Usuń pliki lub foldery utworzone poza warstwami wydajności.

Aby uzyskać więcej informacji, zobacz [Kopiuj do dysków zarządzanych](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wymaganiach systemowych magazynu obiektów Blob urządzenie Data Box](data-box-system-requirements-rest.md).
