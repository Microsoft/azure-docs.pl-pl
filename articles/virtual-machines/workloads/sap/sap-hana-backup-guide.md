---
title: Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines | Microsoft Docs
description: Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA oferuje dwie główne możliwości tworzenia kopii zapasowych dla SAP HANA na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 522af4bf6cc711bbfdfd30d0443ee58dad56b87e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950027"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines

## <a name="getting-started"></a>Wprowadzenie

Przewodnik tworzenia kopii zapasowych dla SAP HANA uruchomionych na maszynach wirtualnych platformy Azure zawiera tylko opis tematów dotyczących platformy Azure. Aby uzyskać ogólne SAP HANA elementów powiązanych z kopią zapasową, zapoznaj się z dokumentacją SAP HANA. Oczekujemy, że znasz zasady tworzenia kopii zapasowych bazy danych, przyczyny i motywacje mające na celu posiadanie solidnej strategii tworzenia kopii zapasowych, a informacje o wymaganiach firmy dotyczą procedury tworzenia kopii zapasowej, okresu przechowywania kopii zapasowych i procedury przywracania.

SAP HANA jest oficjalnie obsługiwana w różnych typach maszyn wirtualnych platformy Azure, takich jak Azure M-Series. Aby zapoznać się z pełną listą SAP HANA certyfikowanych maszyn wirtualnych platformy Azure i jednostek usługi HANA o dużych wystąpieniach, zobacz [Znajdowanie certyfikowanych platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Microsoft Azure oferuje wiele jednostek, w których SAP HANA nie są uruchamiane na serwerach fizycznych. Ta usługa jest nazywana [dużymi wystąpieniami Hana](hana-overview-architecture.md). Ten przewodnik nie obejmuje procesów tworzenia kopii zapasowych i narzędzi dla dużych wystąpień platformy HANA. Jest jednak ograniczona do maszyn wirtualnych platformy Azure. Aby uzyskać szczegółowe informacje na temat procesów tworzenia kopii zapasowej/przywracania z dużymi wystąpieniami platformy HANA, przeczytaj artykuł. [kopia zapasowa i przywracanie](./hana-backup-restore.md)artykułu.

Ten artykuł zawiera trzy możliwości tworzenia kopii zapasowych SAP HANA na maszynach wirtualnych platformy Azure:

- Tworzenie kopii zapasowych HANA za poorednictwem [usług Azure Backup Services](../../../backup/backup-overview.md) 
- Usługa HANA kopia zapasowa w systemie plików na maszynie wirtualnej z systemem Linux (zobacz [SAP HANA Azure Backup na poziomie pliku](sap-hana-backup-file-level.md))
- Tworzenie kopii zapasowych HANA na podstawie migawek magazynu przy użyciu funkcji migawek obiektów BLOB usługi Azure Storage ręcznie lub Azure Backup usługi


SAP HANA oferuje interfejs API tworzenia kopii zapasowych, dzięki któremu narzędzia do tworzenia kopii zapasowych innych firm mogą integrować bezpośrednio z SAP HANA. Produkty takie jak usługa Azure Backup Service lub [CommVault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) używają tego własnościowego interfejsu do wyzwalania SAP HANA bazy danych lub wykonywania kopii zapasowych dziennika. 


Informacje dotyczące sposobu, w jaki można znaleźć oprogramowanie SAP obsługiwane na platformie Azure, można znaleźć w artykule [co to oprogramowanie SAP jest obsługiwane dla wdrożeń platformy Azure](./sap-supported-product-on-azure.md).

## <a name="azure-backup-service"></a>Usługa Azure Backup

Pierwszy przedstawiony scenariusz jest scenariuszem, w którym usługa Azure Backup używa `backint` interfejsu SAP HANA do wykonywania kopii zapasowej przesyłania strumieniowego z bazy danych SAP HANA. Można też użyć bardziej ogólnej funkcji usługi Azure Backup, aby utworzyć migawkę dysku spójną dla aplikacji i przetransferować ją do usługi Azure Backup.

Azure Backup integruje i jest certyfikowane jako rozwiązanie do tworzenia kopii zapasowych dla SAP HANA przy użyciu własnościowego interfejsu SAP HANA o nazwie [BACKINT](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Aby uzyskać więcej informacji na temat rozwiązania, jego możliwości i regionów świadczenia usługi Azure, w których są dostępne, zapoznaj się z artykułem [Obsługa macierzy tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure](../../../backup/sap-hana-backup-support-matrix.md#scenario-support). Aby uzyskać szczegółowe informacje i zasady dotyczące usługi Azure Backup Service for HANA, przeczytaj artykuł [dotyczący SAP HANA kopii zapasowej bazy danych na maszynach wirtualnych platformy Azure](../../../backup/sap-hana-db-about.md). 

Drugą możliwością wykorzystania usługi Azure Backup jest utworzenie kopii zapasowej spójnej na poziomie aplikacji przy użyciu migawek dysków usługi Azure Premium Storage. Inne certyfikowane magazyny platformy Azure, takie jak [Azure Ultra Disk](../../disks-enable-ultra-ssd.md) i [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) , nie obsługują tego rodzaju migawek za pomocą usługi Azure Backup. Odczytywanie następujących artykułów:

- [Planowanie infrastruktury kopii zapasowych maszyny wirtualnej na platformie Azure](../../../backup/backup-azure-vms-introduction.md)
- [Kopia zapasowa maszyn wirtualnych platformy Azure z systemem Linux spójna na poziomie aplikacji](../../../backup/backup-azure-linux-app-consistent.md) 

Ta sekwencja działań okazuje się:

- Azure Backup musi wykonać skrypt poprzedzający migawkę, który umieszcza aplikację w tym przypadku SAP HANA, w stanie spójnym
- Po potwierdzeniu tego spójnego stanu Azure Backup wykona migawki dysku
- Po zakończeniu migawek, Azure Backup spowoduje cofnięcie działania w skrypcie przedmigawkowym
- Po pomyślnym wykonaniu Azure Backup strumieniowe przesyłanie danych do magazynu kopii zapasowych

W przypadku SAP HANA większość klientów korzysta z usługi Azure akcelerator zapisu dla woluminów zawierających dziennik ponownego wykonywania SAP HANA. Usługa Azure Backup automatycznie wykluczają te woluminy z migawek. To wykluczenie nie szkodzi zdolności platformy HANA do przywracania. Mimo że może ona blokować możliwość przywracania z niemal wszystkimi innymi obsługiwanymi przez system SAP systemem DBMS.

Minusem tej możliwości to fakt, że musisz opracować własny skrypt przedmigawkowy. Skrypt przed migawką musi utworzyć migawkę platformy HANA i obsłużyć ostateczne przypadki wyjątków. Skrypt po migawce musi ponownie usunąć migawkę platformy HANA. Aby uzyskać więcej informacji na temat wymaganej logiki, Zacznij od [uwagi pomocy technicznej SAP #2039883](https://launchpad.support.sap.com/#/notes/2039883). Zagadnienia związane z sekcją "SAP HANA spójność danych podczas tworzenia migawek magazynu" w tym artykule mają pełne zastosowanie do tego rodzaju kopii zapasowych.

> [!NOTE]
> Tworzenie kopii zapasowych na podstawie migawek dysków dla SAP HANA w wdrożeniach, w których używane są wiele kontenerów bazy danych, wymagaj minimalnej wersji platformy HANA 2,0 SP04
> 

Szczegółowe informacje na temat migawek magazynu znajdują się w dalszej części tego dokumentu.

![Ten rysunek przedstawia dwie możliwości zapisywania bieżącego stanu maszyny wirtualnej](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Inne metody tworzenia kopii zapasowych HANA
Istnieją trzy inne metody lub ścieżki kopii zapasowej, które mogą być uwzględniane:

- Tworzenie kopii zapasowej w udziale NFS opartym na Azure NetApp Files (ANF). ANF ponownie ma możliwość tworzenia migawek tych woluminów, na których przechowywane są kopie zapasowe. W przypadku przepływności, która ostatecznie wymagała zapisania kopii zapasowych, to rozwiązanie może stać się kosztowną metodą. Chociaż łatwo jest ustanowić, ponieważ platforma HANA może zapisywać kopie zapasowe bezpośrednio w udziale natywnym systemu plików NFS platformy Azure
- Wykonywanie kopii zapasowej HANA na dyskach dołączonych do maszyny wirtualnej SSD w warstwie Standardowa lub Azure Premium Storage. W następnym kroku można skopiować te pliki kopii zapasowej do usługi Azure Blob Storage. Ta strategia może być atrakcyjna dla cen
- Wykonywanie kopii zapasowej HANA na dyskach dołączonych do maszyny wirtualnej SSD w warstwie Standardowa lub Azure Premium Storage. W następnym kroku dysk jest regularnie snapshotted. Po pierwszej migawce migawki przyrostowe mogą służyć do obniżenia kosztów

![Na tym rysunku przedstawiono opcje tworzenia kopii zapasowej pliku SAP HANA w ramach maszyny wirtualnej](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Na tym rysunku przedstawiono opcje tworzenia kopii zapasowej plików SAP HANA w ramach maszyny wirtualnej, a następnie przechowywania plików kopii zapasowych HANA w innym miejscu przy użyciu różnych narzędzi. Jednak wszystkie rozwiązania, które nie obejmują usługi tworzenia kopii zapasowych innych firm ani usługi Azure Backup, mają kilka przeszkód. Niektóre z nich mogą być wymienione na liście, takie jak administracja przechowywania, proces automatycznego przywracania i zapewniające automatyczne odzyskiwanie do określonego momentu jako usługa Azure Backup lub inne wyspecjalizowane zestawy i usługi tworzenia kopii zapasowych innych firm. Wiele z tych usług innych firm może działać na platformie Azure. 


## <a name="sap-resources-for-hana-backup"></a>Zasoby SAP dla kopii zapasowych HANA

### <a name="sap-hana-backup-documentation"></a>Dokumentacja kopii zapasowej SAP HANA

- [Wprowadzenie do administrowania SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planowanie strategii tworzenia kopii zapasowych i odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Zaplanuj tworzenie kopii zapasowych HANA przy użyciu ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Planowanie kopii zapasowych danych (Panel sterowania SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Często zadawane pytania dotyczące SAP HANA kopii zapasowej w programie [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Często zadawane pytania dotyczące SAP HANA baz danych i migawek magazynu w programie [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nieodpowiednie systemy plików sieciowych na potrzeby tworzenia kopii zapasowych i odzyskiwania w programie [SAP uwaga 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Sprawdzanie poprawności SAP HANA kopii zapasowej
Niezależnie od metody tworzenia kopii zapasowej, uruchomienie przywracania testowego w odniesieniu do innego systemu jest bezwzględnie konieczne. Takie podejście umożliwia zapewnienie, że kopia zapasowa jest poprawna, a wewnętrzne procesy tworzenia kopii zapasowych i przywracania działają zgodnie z oczekiwaniami. Chociaż przywracanie kopii zapasowych może być niezależne w środowisku lokalnym ze względu na wymagania dotyczące infrastruktury, znacznie łatwiej jest wykonać w chmurze, zapewniając w ten sposób tymczasowe zasoby. Jest poprawna, ponieważ dostępne są narzędzia z platformą HANA, które umożliwiają przywracanie plików kopii zapasowej. Jednak w celu częstego ćwiczenia przywracania należy przetestować proces przywracania bazy danych i przeszkolić ten proces z pracownikami operacyjnymi.

Należy pamiętać, że wykonywanie prostych operacji przywracania i sprawdzania, czy platformy HANA nie działają i nie są wystarczające. Należy uruchomić sprawdzanie spójności tabeli, aby upewnić się, że przywrócona baza danych jest w prawidłowym obszarze. SAP HANA oferuje kilka rodzajów kontroli spójności opisanych w artykule [SAP uwaga 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informacje o sprawdzaniu spójności tabeli można także znaleźć w witrynie sieci Web SAP w obszarze [Sprawdzanie spójności w tabelach i wykazie](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Zalety i wady tworzenia kopii zapasowych HANA i migawki magazynu

SAP nie&#39;t nadać preferencjom tworzenia kopii zapasowych HANA i magazynu. Zawiera listę ich informatyków i wad, dlatego można określić, która z nich ma być używana w zależności od sytuacji i dostępnej technologii magazynowania (zobacz [Planowanie strategii tworzenia kopii zapasowych i odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

Na platformie Azure należy pamiętać, że funkcja migawek obiektów blob platformy Azure jest niedostępna&#39;t zapewnia spójność systemu plików na wielu dyskach (zobacz [Używanie migawek obiektów BLOB w programie PowerShell](/archive/blogs/cie/using-blob-snapshots-with-powershell)). 

Ponadto jeden z nich musi zrozumieć wpływ rozliczeń podczas pracy często z migawkami obiektów blob, zgodnie z opisem w tym artykule: zrozumienie, w [jaki sposób migawki naliczane są opłaty](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— jest&#39;t, tak jak w przypadku korzystania z usługi Azure Virtual Disks.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA spójności danych podczas tworzenia migawek magazynu

Jak opisano wcześniej, opisywanie funkcji tworzenia kopii zapasowych Azure Backup, systemu plików i spójności aplikacji jest obowiązkowe przy podejmowaniu migawek magazynu. Najprostszym sposobem uniknięcia problemów jest zamknięcie SAP HANA lub nawet całej maszyny wirtualnej. Coś, co nie jest możliwe dla wystąpienia produkcyjnego.

> [!NOTE]
> Tworzenie kopii zapasowych na podstawie migawek dysków dla SAP HANA w wdrożeniach, w których używane są wiele kontenerów bazy danych, wymagaj minimalnej wersji platformy HANA 2,0 SP04
> 

Usługa Azure Storage nie zapewnia spójności systemu plików na wielu dyskach lub woluminach dołączonych do maszyny wirtualnej podczas procesu tworzenia migawki. Oznacza to, że spójność aplikacji podczas tworzenia migawki musi zostać dostarczona przez aplikację, w tym przypadku SAP HANA samej. [Uwaga dotycząca oprogramowania SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883) zawiera ważne informacje dotyczące SAP HANA kopii zapasowych według migawek magazynu. Na przykład w systemach plików XFS należy uruchomić **XFS \_ zamrozić** przed rozpoczęciem migawki magazynu, aby zapewnić spójność aplikacji (zobacz [XFS \_ Zablokuj (8) — Strona Man z systemem Linux](https://linux.die.net/man/8/xfs_freeze) , aby uzyskać szczegółowe informacje na temat **\_ zawieszania XFS**.

Zakładając, że istnieje system plików XFS z czterema dyskami wirtualnymi platformy Azure, następujące kroki zapewniają spójną migawkę, która reprezentuje obszar danych HANA:

1. Utwórz przygotowanie migawki danych platformy HANA
1. Zablokuj systemy plików wszystkich dysków/woluminów (na przykład użyj **XFS \_ Zablokuj**)
1. Utwórz wszystkie wymagane migawki obiektów BLOB na platformie Azure
1. Odblokuj system plików
1. Potwierdzenie migawki danych HANA (spowoduje usunięcie migawki)

Korzystając z możliwości Azure Backup do wykonywania kopii zapasowych migawek spójnych z aplikacjami, kroki #1 muszą być kodowane/skryptowe dla skryptu przedmigawkowego. Usługa Azure Backup wykona kroki #2 i #3. Kroki #4 i #5 muszą zostać ponownie dostarczone przez kod w skrypcie po migawce. Jeśli nie korzystasz z usługi Azure Backup, musisz także zakodować kod/krok skryptu #2 i #3.
Więcej informacji na temat tworzenia migawek danych platformy HANA można znaleźć w następujących artykułach:

- [Migawki danych HANA] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Więcej szczegółów dotyczących wykonywania kroku #1 można znaleźć w artykule [Tworzenie migawki danych (natywne SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Szczegóły dotyczące potwierdzania/usuwania migawek danych platformy HANA zgodnie z potrzebami w kroku #5 można znaleźć w artykule [Tworzenie migawki danych (natywny SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

Ważne jest, aby potwierdzić migawkę HANA. Ze względu na &quot; kopię przy zapisie, &quot; SAP HANA może nie wymagać dodatkowego miejsca na dysku w tym trybie przygotowywania migawek. Nie można również uruchomić nowych kopii zapasowych, dopóki nie zostanie potwierdzone SAP HANA migawka.&#39;


### <a name="sap-hana-backup-scheduling-strategy"></a>Strategia planowania tworzenia kopii zapasowych SAP HANA

W SAP HANA artykule [Planowanie strategii tworzenia kopii zapasowych i odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) stanowi podstawowy plan wykonywania kopii zapasowych. Polegaj na dokumentacji SAP w systemie HANA i Twoich doświadczeniach z innymi systemami DBMS w celu zdefiniowania strategii tworzenia kopii zapasowych/przywracania i procesu SAP HANA. Sekwencja różnych typów kopii zapasowych i okres przechowywania są wysoce zależne od umowy SLA, które należy podać.


### <a name="sap-hana-backup-encryption"></a>SAP HANA szyfrowanie kopii zapasowej

SAP HANA oferuje szyfrowanie danych i dziennika. Jeśli SAP HANA dane i dziennik nie są szyfrowane, kopie zapasowe nie są szyfrowane domyślnie. Jednak SAP HANA oferuje oddzielne szyfrowanie kopii zapasowej zgodnie z opisem w [SAP HANA szyfrowanie kopii zapasowej](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Jeśli używasz starszych wersji SAP HANA, może być konieczne sprawdzenie, czy szyfrowanie kopii zapasowej należało do już podanej funkcji.  


## <a name="next-steps"></a>Następne kroki
* [SAP HANA Azure Backup na poziomie pliku](sap-hana-backup-file-level.md) zawiera opis opcji tworzenia kopii zapasowej opartej na plikach.
* Aby dowiedzieć się, jak stworzyć wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
