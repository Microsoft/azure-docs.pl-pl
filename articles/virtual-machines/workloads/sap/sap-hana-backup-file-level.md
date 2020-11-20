---
title: SAP HANA Azure Backup na poziomie pliku | Microsoft Docs
description: Istnieją dwie istotne możliwości tworzenia kopii zapasowych SAP HANA na maszynach wirtualnych platformy Azure, w tym artykule omówiono SAP HANA Azure Backup na poziomie pliku
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 13bdf4b10ec586bf83a70434f003cc1573aca469
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950265"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup na poziomie pliku

## <a name="introduction"></a>Wprowadzenie

Ten artykuł zawiera pokrewny artykuł dotyczący [podręcznika tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines](./sap-hana-backup-guide.md), który oferuje przegląd oraz informacje na temat rozpoczynania pracy i dalszych szczegółowych informacji na temat Azure Backup migawek usług i magazynów. 

Różne typy maszyn wirtualnych na platformie Azure umożliwiają dołączenie innej liczby wirtualnych dysków twardych. Dokładne szczegóły są udokumentowane w [rozmiarach maszyn wirtualnych z systemem Linux na platformie Azure](../../sizes.md). W przypadku testów, do których odwołuje się ta dokumentacja, używana jest maszyna wirtualna platformy Azure GS5, która umożliwia 64 dołączonych dysków danych. W przypadku większych systemów SAP HANA można już wykonać znaczną liczbę dysków dla plików danych i dziennika, prawdopodobnie w połączeniu z rozłożeniem oprogramowania w celu zapewnienia optymalnej przepływności we/wy dysku. Aby uzyskać więcej informacji na temat sugerowanych konfiguracji dysków dla wdrożeń SAP HANA na maszynach wirtualnych platformy Azure, zapoznaj się z artykułem [SAP HANA konfiguracje magazynu maszyn wirtualnych platformy Azure](./hana-vm-operations-storage.md). Zalecenia zawarte w programie obejmują również zalecenia dotyczące miejsca na dysku dla lokalnych kopii zapasowych.

Standardową metodą zarządzania wykonywaniem kopii zapasowych/przywracania na poziomie plików jest tworzenie kopii zapasowej opartej na plikach za pośrednictwem SAP HANA Studio lub SAP HANA instrukcji SQL. Aby uzyskać więcej informacji, zapoznaj się z artykułem [SAP HANA Dokumentacja usługi SQL i widoki systemowe](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Ten rysunek przedstawia okno dialogowe elementu menu kopia zapasowa w programie SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Ten rysunek przedstawia okno dialogowe elementu menu kopia zapasowa w programie SAP HANA Studio. Po wybraniu typu &quot; plik, &quot; jeden musi określać ścieżkę w systemie plików, gdzie SAP HANA zapisuje pliki kopii zapasowej. Przywracanie działa w ten sam sposób.

Chociaż to rozwiązanie jest proste i proste w przód, istnieją pewne zagadnienia. Maszyna wirtualna platformy Azure ma ograniczenie liczby dysków z danymi, które można dołączać. Przechowywanie plików SAP HANA kopii zapasowych w systemach plików maszyny wirtualnej może nie być możliwe, w zależności od rozmiaru bazy danych i wymagań dotyczących przepływności dysku, co może wiązać się z rozłożeniem oprogramowania na wielu dyskach danych. W dalszej części tego artykułu są dostępne różne opcje przeniesienia tych plików kopii zapasowej oraz zarządzanie ograniczeniami rozmiaru plików i wydajnością podczas obsługi terabajtów danych.

Kolejną opcją, która oferuje większą swobodę dotyczącą całkowitej pojemności, jest usługa Azure Blob Storage. Chociaż pojedynczy obiekt BLOB jest również ograniczony do 1 TB, całkowita pojemność pojedynczego kontenera obiektów BLOB jest obecnie 500 TB. Dodatkowo umożliwia klientom wybór tego, &quot; czy chłodny &quot; Magazyn obiektów blob, który ma korzystny koszt. Aby uzyskać szczegółowe informacje na temat magazynu chłodnych obiektów blob, zobacz [warstwy dostępu gorąca, chłodna i archiwalna](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) .

Aby zachować dodatkowe bezpieczeństwo, użyj konta magazynu zreplikowanego geograficznie do przechowywania kopii zapasowych SAP HANA. Zobacz [nadmiarowość usługi Azure Storage](../../../storage/common/storage-redundancy.md) , aby uzyskać szczegółowe informacje o nadmiarowości magazynu i replikacji magazynu.

Jeden z nich może umieścić dedykowane wirtualne dyski twarde dla SAP HANA kopii zapasowych na dedykowanym koncie magazynu kopii zapasowej, które jest replikowane geograficznie. W przeciwnym razie można skopiować wirtualne dyski twarde, które przechowują kopie zapasowe SAP HANA na koncie magazynu z replikacją geograficzną, lub na konto magazynu znajdujące się w innym regionie.

## <a name="azure-blobxfer-utility-details"></a>Szczegóły narzędzia Azure blobxfer

Przechowywanie katalogów i plików w usłudze Azure Storage może być możliwe za pomocą interfejsu wiersza polecenia lub programu PowerShell lub opracowywania narzędzia przy użyciu jednego z [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/). Do kopiowania danych do usługi Azure Storage jest również dostępne gotowe do użycia narzędzie AzCopy. (zobacz [transfer danych za pomocą narzędzia AzCopy Command-Line](../../../storage/common/storage-use-azcopy-v10.md)).

W związku z tym blobxfer był używany do kopiowania plików kopii zapasowej SAP HANA. Jest to środowisko Open Source używane przez wielu klientów w środowiskach produkcyjnych i dostępne w serwisie [GitHub](https://github.com/Azure/blobxfer). To narzędzie umożliwia jednemu skopiowaniu danych bezpośrednio do usługi Azure Blob Storage lub udziału plików platformy Azure. Oferuje także szereg przydatnych funkcji, takich jak skrót MD5 lub automatyczna równoległość podczas kopiowania katalogów z wieloma plikami.

## <a name="sap-hana-backup-performance"></a>Wydajność tworzenia kopii zapasowych oprogramowania SAP HANA
W tym rozdziale omówiono zagadnienia dotyczące wydajności. Liczby uzyskane mogą nie reprezentować najnowszego stanu, ponieważ istnieje stały rozwój do osiągnięcia lepszej przepływności do usługi Azure Storage. W związku z tym należy wykonać indywidualne testy dotyczące konfiguracji i regionu platformy Azure.

![Ten zrzut ekranu jest SAP HANA konsoli zapasowej w programie SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Ten zrzut ekranu przedstawia konsolę SAP HANA Backup programu SAP HANA Studio. Wykonanie kopii zapasowej 230 GB na pojedynczym dysku usługi Azure HDD w warstwie Standardowa Storage podłączonym do maszyny wirtualnej HANA przy użyciu systemu plików XFS na jednym dysku trwało około 42 minut.

![Ten zrzut ekranu jest YaST SAP HANA na testowej maszynie wirtualnej](media/sap-hana-backup-file-level/one-backup-disk.png)

Ten zrzut ekranu jest YaST SAP HANA na testowej maszynie wirtualnej. Można zobaczyć pojedynczy dysk o pojemności 1 TB do SAP HANA kopii zapasowej. Wykonanie kopii zapasowej 230 GB trwało około 42 minut. Ponadto dyski 5 200-GB zostały dołączone i utworzono oprogramowanie RAID MD0 w systemie, w którym znajduje się na tym pięciu dyskach danych platformy Azure.

![Powtarzanie tej samej kopii zapasowej na platformie RAID oprogramowania z rozłożonymi na pięć dołączonych dysków z danymi usługi Azure Standard Storage](media/sap-hana-backup-file-level/five-backup-disks.png)

Powtarzanie tej samej kopii zapasowej na woluminie RAID oprogramowania z rozłożonymi na pięć podłączonych dysków z danymi magazynu w warstwie Standardowa platformy Azure spowodowało czas wykonywania kopii zapasowej od 42 minut do 10 minut. Dyski zostały dołączone bez buforowania do maszyny wirtualnej. W tym ćwiczeniu przedstawiono znaczenie przepływności zapisu na dysku w celu uzyskania dobrego czasu tworzenia kopii zapasowej. Możesz przełączyć się do usługi Azure SSD w warstwie Standardowa Storage lub Azure Premium Storage, aby przyspieszyć proces optymalnej wydajności. Ogólnie rzecz biorąc, usługa Azure Standard Storage nie jest zalecana i została użyta tylko do celów demonstracyjnych. Zaleca się użycie co najmniej platformy Azure SSD w warstwie Standardowa Storage lub Azure Premium Storage dla systemów produkcyjnych.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopiowanie plików SAP HANA kopii zapasowej do usługi Azure Blob Storage
Numery wydajności, numery czasu trwania kopii zapasowych i numery okresów kopiowania mogą nie reprezentować najnowszego stanu technologii platformy Azure. Firma Microsoft stale ulepsza usługę Azure Storage, aby zapewnić większą przepływność i krótsze opóźnienia. W związku z tym liczby służą wyłącznie do celów demonstracyjnych. Musisz przetestować swój indywidualną potrzebę w wybranym regionie świadczenia usługi Azure, aby móc ocenić przy użyciu metody.

Innym rozwiązaniem jest szybkie przechowywanie SAP HANA plików kopii zapasowej w usłudze Azure Blob Storage. Jeden kontener obiektów BLOB ma limit około 500 TB, wystarczający dla systemów SAP HANA, przy użyciu M32ts, M32ls, M64ls i GS5 maszyn wirtualnych z systemem Azure, aby zachować wystarczającą SAP HANA kopii zapasowych. Klienci mają możliwość wyboru między &quot; gorącą &quot; i &quot; zimną &quot; magazynem obiektów BLOB (zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)).

Za pomocą narzędzia blobxfer można łatwo kopiować pliki kopii zapasowej SAP HANA bezpośrednio do usługi Azure Blob Storage.

![W tym miejscu można zobaczyć pliki pełnej kopii zapasowej pliku SAP HANA](media/sap-hana-backup-file-level/list-of-backups.png)

Można wyświetlić pliki pełnej kopii zapasowej pliku SAP HANA. W przypadku czterech plików największy rozmiar ma wielkość 230 GB.

![Kopiowanie 230 GB do kontenera obiektów BLOB usługi Azure Standard Storage trwało około 3000 sekund](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Użycie skrótu MD5 w teście wstępnym trwało około 3000 sekund na skopiowanie 230 GB do kontenera obiektów BLOB konta magazynu w warstwie Standardowa Azure.

Konsola tworzenia kopii zapasowych HANA Studio pozwala na ograniczenie maksymalnego rozmiaru plików kopii zapasowych HANA. W przykładowym środowisku Ulepszona wydajność przez posiadanie wielu mniejszych plików kopii zapasowej zamiast jednego dużego pliku 230 GB.

Ustawienie limitu rozmiaru pliku kopii zapasowej po stronie HANA nie&#39;t poprawić czasu wykonywania kopii zapasowej, ponieważ pliki są zapisywane sekwencyjnie. Limit rozmiaru pliku został ustawiony na 60 GB, więc kopia zapasowa stworzyła cztery duże pliki danych zamiast jednego pliku 230-GB. Korzystanie z wielu plików kopii zapasowej może być konieczna do tworzenia kopii zapasowych baz danych HANA, jeśli obiekty docelowe kopii zapasowych mają ograniczenia dotyczące rozmiarów plików obiektów BLOB.

![Aby przetestować równoległość narzędzia blobxfer, maksymalny rozmiar plików dla kopii zapasowych HANA został następnie ustawiony na 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Aby przetestować równoległość narzędzia blobxfer, maksymalny rozmiar plików dla kopii zapasowych HANA został następnie ustawiony na 15 GB, co spowodowało 19 plików kopii zapasowej. Ta konfiguracja umożliwia blobxfer do kopiowania 230 GB do magazynu obiektów blob platformy Azure z 3000 sekund do 875 sekund.

Podczas przeglądania kopii zapasowych wykonywanych na dyskach lokalnych w innych lokalizacjach, takich jak usługa Azure Blob Storage, należy pamiętać, że przepustowość wykorzystywana przez proces równoległego kopiowania jest rozliczana względem przydziału sieci lub magazynu dla poszczególnych typów maszyn wirtualnych. W związku z tym należy zrównoważyć czas trwania kopiowania do sieci i przepustowości magazynu, gdy wymagane jest normalne obciążenie uruchomione na maszynie wirtualnej. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopiowanie plików kopii zapasowej SAP HANA do udziału NFS

Microsoft Azure oferować natywne udziały NFS przy użyciu [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). W celu przechowywania kopii zapasowych i zarządzania nimi można tworzyć różne woluminy o dużej pojemności. Można również utworzyć migawkę tych woluminów na podstawie technologii NetApp. Azure NetApp Files (ANF) jest oferowany na trzech różnych poziomach usługi, które zapewniają inną przepływność magazynu. Aby uzyskać więcej informacji, zapoznaj się z artykułem [poziomy usługi dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Wolumin systemu plików NFS można utworzyć i zainstalować z ANF zgodnie z opisem w artykule [Szybki Start: konfigurowanie Azure NetApp Files i Tworzenie woluminu NFS](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal).

Oprócz korzystania z natywnych woluminów NFS systemu Azure za pośrednictwem programu ANF istnieją różne możliwości tworzenia wdrożeń, które udostępniają udziały NFS na platformie Azure. Wszyscy mają wady, które należy wykonać, aby samodzielnie wdrażać te rozwiązania i zarządzać nimi. Niektóre z tych możliwości zostały udokumentowane w tych artykułach:

- [Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- [System GlusterFS na maszynach wirtualnych platformy Azure z systemem Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)

Udziały NFS utworzone w sposób opisany powyżej mogą służyć do bezpośredniego wykonywania kopii zapasowych platformy HANA względem lub do kopiowania kopii zapasowych wykonanych na dyskach lokalnych do tych udziałów systemu plików NFS.

> [!NOTE]
> SAP HANA obsługują system plików NFS v3 i system plików NFS v4. x. Każdy inny format, taki jak SMB z systemem plików CIFS, nie jest obsługiwany w przypadku zapisywania kopii zapasowych HANA. Zobacz również temat [Pomoc techniczna SAP uwagi #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopiowanie SAP HANA plików kopii zapasowej do Azure Files

Możliwe jest zainstalowanie udziału Azure Files w ramach maszyny wirtualnej z systemem Linux na platformie Azure. Artykuł dotyczący [sposobu korzystania z usługi Azure File Storage z systemem Linux](../../../storage/files/storage-how-to-use-files-linux.md) zawiera szczegółowe informacje na temat sposobu przeprowadzania konfiguracji. Aby uzyskać ograniczenia dotyczące plików Azure Files lub Azure Premium, przeczytaj artykuł [Azure Files cele dotyczące skalowalności i wydajności](../../../storage/files/storage-files-scale-targets.md).

> [!NOTE]
> Funkcja SMB z systemem plików CIFS nie jest obsługiwana przez SAP HANA do zapisywania kopii zapasowych HANA. Zobacz również temat [Pomoc techniczna SAP uwagi #1820529](https://launchpad.support.sap.com/#/notes/1820529). W związku z tym można używać tego rozwiązania jako ostatecznego miejsca docelowego kopii zapasowej bazy danych platformy HANA, która została przeprowadzona bezpośrednio względem lokalnych dołączonych dysków
> 

W teście w odniesieniu do Azure Files, nie plików platformy Azure w warstwie Premium, trwało około 929 sekund na skopiowanie 19 plików kopii zapasowej z całkowitą ilością 230 GB. Oczekujemy, że czas korzystania z plików systemu Azure Premium jest lepszy. Należy jednak pamiętać, że trzeba zrównoważyć interesy szybkiej kopii z wymaganiami obciążeń sieci. Ponieważ każdy typ maszyny wirtualnej platformy Azure wymusza limit przepustowości sieci, należy pozostać w zakresie tego przydziału wraz z obciążeniem i kopią plików kopii zapasowej.

![Na tym rysunku przedstawiono około 929 sekund na skopiowanie 19 SAP HANA plików kopii zapasowej](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Przechowywanie SAP HANA plików kopii zapasowej w usłudze Azure Files może być ciekawą opcją. Szczególnie w przypadku ulepszonego opóźnienia i przepływności plików platformy Azure w warstwie Premium.

## <a name="next-steps"></a>Następne kroki
* [Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines](sap-hana-backup-guide.md) zawiera omówienie i informacje na temat rozpoczynania pracy.
* Aby dowiedzieć się, jak stworzyć wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).