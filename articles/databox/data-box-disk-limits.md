---
title: Limity Azure Data Box Disk | Microsoft Docs
description: Opisuje limity systemowe i zalecane rozmiary Data Box Disk Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: e003d0121721838bd5ae038a3a8b4d1b8cd9d1eb
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573194"
---
# <a name="azure-data-box-disk-limits"></a>Limity Azure Data Box Disk


Te limity należy wziąć pod uwagę podczas wdrażania i obsługi rozwiązania Data Box Disk Microsoft Azure.

## <a name="data-box-service-limits"></a>Limity usługi urządzenie Data Box

 - Usługa urządzenie Data Box jest dostępna w regionach świadczenia usługi Azure na liście [dostępności regionów](data-box-disk-overview.md#region-availability).
 - W Data Box Disk jest obsługiwane jedno konto magazynu.

## <a name="data-box-disk-performance"></a>Data Box Disk wydajność

Podczas testowania dysków podłączonych za pomocą portu USB 3.0 ich wydajność doszła do poziomu 430 MB/s. Rzeczywista wartość różni się w zależności od rozmiaru pliku. Przy mniejszych plikach wydajność może być niższa.

## <a name="azure-storage-limits"></a>Limity usługi Azure Storage

Ta sekcja zawiera opis limitów usługi Azure Storage oraz wymaganych konwencji nazewnictwa dla Azure Files, blokowych obiektów blob platformy Azure i obiektów BLOB na stronie platformy Azure, które dotyczą usługi urządzenie Data Box. Uważnie Przejrzyj limity magazynu i postępuj zgodnie ze wszystkimi zaleceniami.

Najnowsze informacje na temat limitów usługi Azure Storage i najlepszych rozwiązań dotyczących nazewnictwa udziałów, kontenerów i plików można znaleźć w temacie:

- [Nazewnictwo i kontenery odwołań](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nazywanie i przywoływanie udziałów](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Zablokuj obiekty blob i konwencje obiektów BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Jeśli istnieją pliki lub katalogi, które przekraczają limity usługi Azure Storage lub nie są zgodne z konwencjami nazewnictwa Azure Files/obiektów blob, te pliki lub katalogi nie są pozyskiwane w usłudze Azure Storage za pośrednictwem usługi urządzenie Data Box.

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

- Nie należy kopiować danych bezpośrednio do dysków. Skopiuj dane do wstępnie utworzonych folderów *BlockBlob*,*PageBlob* i *AzureFile* .
- Folder w *BlockBlob* i *PageBlob* jest kontenerem. Na przykład kontenery są tworzone jako *BlockBlob/Container* i *PageBlob/Container*.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt BLOB) w chmurze o takiej samej nazwie jak kopiowany obiekt, Data Box Disk zmieni nazwę pliku jako plik (1) w chmurze.
- Każdy plik zapisany w udziałach *BlockBlob* i *PageBlob* jest przekazywany jako blokowy obiekt BLOB i stronicowy obiekt BLOB.
- Żadna pusta hierarchia katalogów (bez plików) utworzona w folderach *BlockBlob* i *PageBlob* nie zostanie przekazana.
- Jeśli wystąpią błędy podczas przekazywania danych do platformy Azure, na docelowym koncie magazynu zostanie utworzony dziennik błędów. Ścieżka do tego dziennika błędów jest dostępna w portalu, gdy przekazywanie zostało zakończone, i można przejrzeć dziennik, aby podjąć działania naprawcze. Nie należy usuwać danych ze źródła bez weryfikowania przekazanych danych.
- Metadane plików i uprawnienia NTFS nie są zachowywane, gdy dane są przekazywane do Azure Files. Na przykład *ostatni zmodyfikowany* atrybut plików nie będzie przechowywany podczas kopiowania danych.
- Jeśli w kolejności określono dyski zarządzane, należy zapoznać się z następującymi kwestiami dodatkowymi:

    - Można mieć tylko jeden dysk zarządzany o danej nazwie w grupie zasobów we wszystkich wstępnie utworzonych folderach i na wszystkich urządzeniach Data Box Disk. Oznacza to, że wirtualne dyski twarde przekazane do wstępnie utworzonych folderów powinny mieć unikatowe nazwy. Upewnij się, że dana nazwa nie jest zgodna z nazwą już istniejącego dysku zarządzanego w grupie zasobów. Jeśli wirtualne dyski twarde mają takie same nazwy, tylko jeden z nich zostanie przekonwertowany na dysk zarządzany o tej nazwie. Pozostałe wirtualne dyski twarde zostaną przekazane do przejściowego konta magazynu jako stronicowe obiekty blob.
    - Zawsze kopiuj wirtualne dyski twarde do jednego ze wstępnie utworzonych folderów. Jeśli skopiujesz dyski VHD do lokalizacji innej niż te foldery lub do folderu utworzonego przez siebie, wirtualne dyski twarde zostaną przekazane do konta usługi Azure Storage jako stronicowe obiekty blob i dyski niezarządzane.
    - Na potrzeby tworzenia dysków zarządzanych można przekazywać tylko stałe wirtualne dyski twarde. Dynamiczne dyski VHD, różnicowe dyski VHD i pliki VHDX nie są obsługiwane.
    - Pliki inne niż VHD skopiowane do folderów z pretworzonym dyskiem zarządzanym nie zostaną przekonwertowane na dysk zarządzany.

## <a name="azure-storage-account-size-limits"></a>Limity rozmiaru konta usługi Azure Storage

Poniżej przedstawiono limity rozmiaru danych, które można skopiować na konto magazynu. Upewnij się, że przekazane dane są zgodne z tymi limitami. 

| Typ danych             | Limit domyślny          |
|--------------------------|------------------------|
| blokowy obiekt BLOB, stronicowy obiekt BLOB    | Aby uzyskać aktualne informacje dotyczące tych limitów, zobacz [cele skalowania magazynu obiektów blob platformy Azure](../storage/blobs/scalability-targets.md#scale-targets-for-blob-storage), [elementy docelowe skalowania w warstwie Standardowa platformy Azure](../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts)oraz [miejsce docelowe skalowania Azure Files](../storage/files/storage-files-scale-targets.md#file-share-and-file-scale-targets). <br /><br /> Limity obejmują dane ze wszystkich źródeł, w tym Data Box Disk.|


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie przekazane pliki są zgodne z tymi limitami.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt BLOB        | ~ 4,75 TiB                                                 |
| Obiekt BLOB strony         | 8 TiB <br> (Każdy plik przekazany w formacie stronicowego obiektu BLOB musi być wyrównany do 512 bajtów, w przeciwnym razie przekazywanie nie powiedzie się. <br> Pliki VHD i VHDX są wyrównane do 512 bajtów. |
|Azure Files        | 1 TiB <br> Maksymalnie z rozmiar udziału to 5 TiB     |
| Dyski zarządzane     |4 TiB <br> Aby uzyskać więcej informacji na temat rozmiaru i limitów, zobacz: <li>[Elementy docelowe skalowalności dla dysków zarządzanych](../virtual-machines/disks-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Blokowe obiekty blob platformy Azure, stronicowe obiekty blob i konwencje nazewnictwa plików

| Jednostka                                       | Konwencje                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nazwy kontenerów dla blokowych obiektów blob i stronicowych obiektów BLOB <br> Nazwy plików udziału dla Azure Files | Musi być prawidłową nazwą DNS o długości od 3 do 63 znaków. <br>  Musi zaczynać się literą lub cyfrą. <br> Może zawierać tylko małe litery, cyfry i łącznik (-). <br> Bezpośrednio przed łącznikiem (-) i bezpośrednio po nim musi znajdować się cyfra lub litera. <br> Nazwy nie mogą zawierać sąsiadujących ze sobą łączników. |
| Nazwy katalogów i plików dla usługi Azure Files     |<li> Zachowywanie wielkości liter, bez uwzględniania wielkości liter i nie może przekraczać 255 znaków. </li><li> Nie może kończyć się ukośnikiem (/). </li><li>Jeśli jest podany, zostanie automatycznie usunięta. </li><li> Następujące znaki są niedozwolone: <code>" \\ / : \| < > * ?</code></li><li> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li> Nieprawidłowe znaki ścieżki URL są niedozwolone. Punkty kodu, takie jak \\ uE000, nie są prawidłowymi znakami Unicode. Niektóre znaki ASCII lub Unicode, takie jak znaki sterujące (0x00 do 0x1F, \\ u0081 itp.), również są niedozwolone. Aby uzyskać reguły dotyczące ciągów Unicode w protokole HTTP/1.1, zobacz RFC 2616, sekcja 2,2: Basic rules i RFC 3987. </li><li> Następujące nazwy plików są niedozwolone: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, znak kropki (.) i dwa znaki kropki (..).</li>|
| Nazwy blokowych i stronicowych obiektów blob      | W nazwach obiektów blob jest rozróżniana wielkość liter. Mogą zawierać dowolną kombinację znaków. <br> Nazwa obiektu blob musi zawierać od 1 do 1024 znaków. <br> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. <br>Liczba segmentów ścieżki w nazwie obiektu blob nie może przekraczać 254. Segment ścieżki to ciąg znajdujący się pomiędzy następującymi po sobie znakami ogranicznika (na przykład ukośnikami „/”), co odpowiada nazwie katalogu wirtualnego. |

## <a name="managed-disk-naming-conventions"></a>Konwencje nazewnictwa dysków zarządzanych

| Jednostka | Konwencje                                             |
|-------------------|-----------------------------------------------------------|
| Nazwy dysków zarządzanych       | <li> Nazwa musi mieć od 1 do 80 znaków. </li><li> Nazwa musi zaczynać się literą lub cyfrą, kończyć literą, cyfrą lub podkreśleniem. </li><li> Nazwa może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. </li><li>   Nazwa nie powinna zawierać spacji ani `/` .                                              |

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wymagania systemowe Data Box Disk](data-box-disk-system-requirements.md)