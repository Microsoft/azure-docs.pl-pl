---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a662598efdca05769c7da9fbeecdf692dccdacb5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027240"
---
## <a name="overview"></a>Omówienie
Usługa Azure Storage oferuje możliwość tworzenia migawek obiektów BLOB. Migawki przechwytują stan obiektu BLOB w tym momencie. W tym artykule opisano scenariusz, w którym można zachować kopie zapasowe dysków maszyny wirtualnej za pomocą migawek. Tej metody można użyć w przypadku wybrania opcji nie używać usługi Azure Backup i odzyskiwania oraz chcesz utworzyć niestandardową strategię tworzenia kopii zapasowych dla dysków maszyny wirtualnej. W przypadku maszyn wirtualnych z podstawowymi obciążeniami biznesowymi lub strategicznymi zaleca się używanie [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) w ramach strategii tworzenia kopii zapasowych.  

Dyski maszyny wirtualnej platformy Azure są przechowywane jako stronicowe obiekty blob w usłudze Azure Storage. Ze względu na to, że w tym artykule opisano strategię tworzenia kopii zapasowych dla dysków maszyny wirtualnej, odwołujemy się do migawek w kontekście stronicowych obiektów BLOB. Aby dowiedzieć się więcej na temat migawek, zobacz [Tworzenie migawki obiektu BLOB](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Co to jest migawka?
Migawka obiektu BLOB to wersja obiektu BLOB tylko do odczytu, która jest przechwytywana w punkcie w czasie. Po utworzeniu migawki można ją odczytywać, kopiować lub usuwać, ale nie modyfikować. Migawki umożliwiają utworzenie kopii zapasowej obiektu BLOB, który pojawia się w momencie. Do momentu, w którym jest w wersji 2015-04-05, masz możliwość kopiowania pełnych migawek. W przypadku wersji REST 2015-07-08 i nowszych można także skopiować migawki przyrostowe.

## <a name="full-snapshot-copy"></a>Pełna kopia migawek
Migawki można skopiować na inne konto magazynu jako obiekt BLOB, aby zachować kopie zapasowe podstawowego obiektu BLOB. Migawkę można także skopiować na jej bazowy obiekt BLOB, co przypomina przywrócenie obiektu BLOB do wcześniejszej wersji. Gdy migawka jest kopiowana z jednego konta magazynu do innego, zajmuje to samo miejsce, co obiekt BLOB strony podstawowej. W związku z tym kopiowanie całych migawek z jednego konta magazynu do innego jest wolne i zużywa dużo miejsca na docelowym koncie magazynu.

> [!NOTE]
> Jeśli skopiujesz podstawowy obiekt BLOB do innego miejsca docelowego, migawki obiektu BLOB nie zostaną skopiowane razem z nim. Podobnie, jeśli zastąpisz podstawowy obiekt BLOB z kopią, nie ma to zmian dla migawek skojarzonych z podstawowym obiektem BLOB, a ich nie pozostanie bez zmian w nazwie podstawowego obiektu BLOB.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Tworzenie kopii zapasowych dysków przy użyciu migawek
Jako strategia tworzenia kopii zapasowych dla dysków maszyny wirtualnej można okresowo tworzyć migawki dysku lub stronicowego obiektu BLOB oraz kopiować je do innego konta magazynu za pomocą narzędzi, takich jak [copy BLOB](/rest/api/storageservices/Copy-Blob) Operation lub [AzCopy](../articles/storage/common/storage-use-azcopy-v10.md). Migawkę można skopiować do docelowego obiektu BLOB strony z inną nazwą. Docelowy obiekt BLOB strony docelowej jest zapisywalnym obiektem BLOB strony, a nie z migawką. W dalszej części tego artykułu opisano kroki tworzenia kopii zapasowych dysków maszyny wirtualnej za pomocą migawek.

### <a name="restore-disks-using-snapshots"></a>Przywracanie dysków przy użyciu migawek
Gdy czas przywracania dysku do stabilnej wersji, która została wcześniej przechwycona w jednej z migawek kopii zapasowych, można skopiować migawkę za pośrednictwem obiektu BLOB strony podstawowej. Po podwyższeniu poziomu migawki do obiektu BLOB strony podstawowej migawka pozostaje, ale jego źródło jest zastępowane kopią, którą można odczytać i zapisać. W dalszej części tego artykułu opisano kroki umożliwiające przywrócenie poprzedniej wersji dysku z jego migawki.

### <a name="implementing-full-snapshot-copy"></a>Implementowanie pełnej kopii migawek
Możesz zaimplementować pełną kopię migawki, wykonując następujące czynności:

* Najpierw utwórz migawkę podstawowego obiektu BLOB przy użyciu operacji [obiektu BLOB migawki](/rest/api/storageservices/Snapshot-Blob) .
* Następnie skopiuj migawkę na docelowe konto magazynu przy użyciu polecenia [copy BLOB](/rest/api/storageservices/Copy-Blob).
* Powtórz ten proces, aby zachować kopie zapasowe podstawowego obiektu BLOB.

## <a name="incremental-snapshot-copy"></a>Przyrostowa kopia migawek
Nowa funkcja w interfejsie API [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) zapewnia znacznie lepszy sposób tworzenia kopii zapasowej migawek obiektów blob lub dysków stron. Interfejs API zwraca listę zmian między bazowym obiektem blob a migawkami, co zmniejsza ilość miejsca do magazynowania używanego na koncie kopii zapasowej. Interfejs API obsługuje stronicowe obiekty blob na Premium Storage jak również w przypadku magazynu w warstwie Standardowa. Korzystając z tego interfejsu API, można tworzyć szybsze i wydajniejsze rozwiązania do tworzenia kopii zapasowych dla maszyn wirtualnych platformy Azure. Ten interfejs API będzie dostępny w wersji REST 2015-07-08 lub nowszej.

Przyrostowa kopia migawek umożliwia skopiowanie z jednego konta magazynu do innej różnicy między,

* Podstawowy obiekt BLOB i jego migawka lub
* Wszystkie dwie migawki podstawowego obiektu BLOB

Pod warunkiem spełnienia następujących warunków:

* Obiekt BLOB został utworzony w sty-1-2016 lub nowszej.
* Obiekt BLOB nie został zastąpiony [PutPage](/rest/api/storageservices/Put-Page) lub [Kopiuj obiekt BLOB](/rest/api/storageservices/Copy-Blob) między dwiema migawkami.

>[!NOTE]
>Ta funkcja jest dostępna dla warstwowych obiektów BLOB w warstwie Premium i Standardowa platformy Azure.

Jeśli masz niestandardową strategię tworzenia kopii zapasowych przy użyciu migawek, kopiowanie migawek z jednego konta magazynu do innego może być wolne i może zużywać dużo miejsca w magazynie. Zamiast kopiować całą migawkę do konta magazynu kopii zapasowych, można napisać różnicę między kolejnymi migawkami do obiektu BLOB strony kopii zapasowej. W ten sposób czas kopiowania i miejsce do przechowywania kopii zapasowych znacznie zmniejsza się.

### <a name="implementing-incremental-snapshot-copy"></a>Implementowanie przyrostowej kopii migawek
Można zaimplementować przyrostową kopię migawki, wykonując następujące czynności:

* Utwórz migawkę podstawowego obiektu BLOB przy użyciu [obiektu BLOB migawki](/rest/api/storageservices/Snapshot-Blob).
* Skopiuj migawkę do docelowego konta magazynu kopii zapasowych w tym samym lub innym regionie świadczenia usługi Azure przy użyciu funkcji [kopiowania obiektów BLOB](/rest/api/storageservices/Copy-Blob). To jest obiekt BLOB strony kopii zapasowej. Utwórz migawkę obiektu BLOB strony kopii zapasowej i Zapisz go na koncie kopii zapasowej.
* Zrób kolejną migawkę podstawowego obiektu BLOB przy użyciu obiektu BLOB Snapshot.
* Pobierz różnicę między pierwszą i drugą migawką podstawowego obiektu BLOB przy użyciu [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges). Użyj nowego parametru **prevsnapshot**, aby określić wartość DateTime migawki, z którą chcesz uzyskać różnicę. Gdy ten parametr jest obecny, odpowiedź REST obejmuje tylko strony, które zostały zmienione między migawką docelową i poprzednią migawką, w tym jasne strony.
* Użyj [PutPage](/rest/api/storageservices/Put-Page) , aby zastosować te zmiany do obiektu BLOB strony kopii zapasowej.
* Na koniec Utwórz migawkę obiektu BLOB strony kopii zapasowej i Zapisz go na koncie magazynu kopii zapasowej.

W następnej sekcji opisano szczegółowo, jak można zachować kopie zapasowe dysków przy użyciu przyrostowej kopii migawek

## <a name="scenario"></a>Scenariusz
W tej sekcji opisano scenariusz, który obejmuje niestandardową strategię tworzenia kopii zapasowych dla dysków maszyny wirtualnej za pomocą migawek.

Rozważmy maszynę wirtualną platformy Azure z serii DS z dołączonym dyskiem usługi Premium Storage P30. Dysk P30 o nazwie *mypremiumdisk* jest przechowywany w ramach konta magazynu w warstwie Premium o nazwie *mypremiumaccount*. Konto magazynu w warstwie Standardowa o nazwie *mybackupstdaccount* jest używane do przechowywania kopii zapasowych *mypremiumdisk*. Chcemy zachować migawkę *mypremiumdisk* co 12 godzin.

Aby dowiedzieć się więcej na temat tworzenia konta magazynu, zobacz [Tworzenie konta magazynu](../articles/storage/common/storage-account-create.md).

Aby dowiedzieć się więcej o tworzeniu kopii zapasowych maszyn wirtualnych platformy Azure, zobacz [Planowanie kopii zapasowych maszyn wirtualnych platformy Azure](../articles/backup/backup-azure-vms-introduction.md)

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Procedura obsługi kopii zapasowych dysku przy użyciu migawek przyrostowych
W poniższych krokach opisano sposób tworzenia migawek *mypremiumdisk* i zarządzania kopiami zapasowymi w *mybackupstdaccount*. Kopia zapasowa jest standardowym obiektem BLOB na stronie o nazwie *mybackupstdpageblob*. Obiekt BLOB strony kopii zapasowej zawsze odzwierciedla ten sam stan co Ostatnia migawka *mypremiumdisk*.

1. Utwórz obiekt BLOB strony kopii zapasowej dla dysku magazynu Premium, pobierając migawkę *mypremiumdisk* o nazwie *mypremiumdisk_ss1*.
2. Skopiuj tę migawkę do mybackupstdaccount jako obiekt BLOB stronicowania o nazwie *mybackupstdpageblob*.
3. Utwórz migawkę *mybackupstdpageblob* o nazwie *Mybackupstdpageblob_ss1* przy użyciu [obiektu BLOB migawki](/rest/api/storageservices/Snapshot-Blob) i Zapisz ją w *mybackupstdaccount*.
4. W oknie Kopia zapasowa Utwórz kolejną migawkę *mypremiumdisk*, powiedz *mypremiumdisk_ss2* i Zapisz ją w *mypremiumaccount*.
5. Pobierz przyrostowe zmiany między dwiema migawkami, *mypremiumdisk_ss2* i *Mypremiumdisk_ss1*, używając [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) na *mypremiumdisk_ss2* z parametrem **prevsnapshot** ustawionym na sygnaturę czasową *mypremiumdisk_ss1*. Zapisuj te zmiany przyrostowe w obiekcie blob *mybackupstdpageblob* na stronie kopia zapasowa w *mybackupstdaccount*. Jeśli w przyrostowych zmianach zostały usunięte zakresy, muszą one zostać wyczyszczone z obiektu BLOB strony kopii zapasowej. Użyj [PutPage](/rest/api/storageservices/Put-Page) , aby napisać przyrostowe zmiany do obiektu BLOB strony kopii zapasowej.
6. Utwórz migawkę obiektu BLOB *mybackupstdpageblob* na stronie kopii zapasowej o nazwie *mybackupstdpageblob_ss2*. Usuń poprzednią *mypremiumdisk_ss1* migawki z konta magazynu w warstwie Premium.
7. Powtórz kroki 4-6 dla każdego okna kopii zapasowej. W ten sposób można zachować kopie zapasowe *mypremiumdisk* na koncie magazynu w warstwie Standardowa.

![Tworzenie kopii zapasowej dysku przy użyciu migawek przyrostowych](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Procedura przywracania dysku z migawek
Poniższe kroki opisują sposób przywracania dysku Premium, *mypremiumdisk* do wcześniejszej migawki z konta magazynu kopii zapasowych *mybackupstdaccount*.

1. Zidentyfikuj punkt w czasie, do którego chcesz przywrócić dysk w warstwie Premium. Załóżmy, że jest to migawka *mybackupstdpageblob_ss2*, która jest przechowywana na koncie magazynu kopii zapasowej *mybackupstdaccount*.
2. W programie mybackupstdaccount Podwyższ poziom migawki *mybackupstdpageblob_ss2* jako nowy obiekt BLOB strony podstawowej kopii zapasowej *mybackupstdpageblobrestored*.
3. Utwórz migawkę obiektu BLOB przywróconej strony kopii zapasowej o nazwie *mybackupstdpageblobrestored_ss1*.
4. Skopiuj przywrócony obiekt BLOB Page *mybackupstdpageblobrestored* z *mybackupstdaccount* do *mypremiumaccount* jako nowy dysk w warstwie Premium *mypremiumdiskrestored*.
5. Utwórz migawkę *mypremiumdiskrestored* o nazwie *mypremiumdiskrestored_ss1* do tworzenia przyszłych przyrostowych kopii zapasowych.
6. Wskaż maszynę wirtualną z serii DS przywróconego dysku *mypremiumdiskrestored* i odłącz stare *mypremiumdisk* z maszyny wirtualnej.
7. Rozpocznij proces tworzenia kopii zapasowej opisany w poprzedniej sekcji dla przywróconego dysku *mypremiumdiskrestored*, używając *mybackupstdpageblobrestored* jako obiektu BLOB strony kopii zapasowej.

![Przywróć dysk z migawek](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Następne kroki
Skorzystaj z poniższych linków, aby dowiedzieć się więcej o tworzeniu migawek obiektu BLOB i planowaniu infrastruktury kopii zapasowych maszyny wirtualnej.

* [Tworzenie migawki obiektu BLOB](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planowanie infrastruktury kopii zapasowych maszyny wirtualnej](../articles/backup/backup-azure-vms-introduction.md)