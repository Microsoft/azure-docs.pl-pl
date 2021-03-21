---
title: Migrowanie bazy danych SQL Server do SQL Server na maszynie wirtualnej | Microsoft Docs
description: Dowiedz się więcej o tym, jak migrować lokalną bazę danych użytkownika do SQL Server na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f6e9009040d2d02702f8a71c352716491d07d1f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98704308"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>Migrowanie bazy danych SQL Server do SQL Server na maszynie wirtualnej platformy Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Istnieje kilka sposobów migrowania lokalnej bazy danych SQL Server użytkownika do SQL Server na maszynie wirtualnej platformy Azure. Ten artykuł zawiera krótkie omówienie różnych metod i zaleca najlepszą metodę dla różnych scenariuszy.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 i SQL Server 2008 R2 zbliżają się do [końca cyklu życia pomocy technicznej](https://www.microsoft.com/sql-server/sql-server-2008) dla wystąpień lokalnych. Aby rozszerzyć obsługę, można przeprowadzić migrację wystąpienia SQL Server na maszynę wirtualną platformy Azure lub zakupić rozszerzone aktualizacje zabezpieczeń, aby zachować je w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz sekcję [dotyczącą rozszerzeń dla SQL Server 2008 i 2008 R2 z platformą Azure](sql-server-2008-extend-end-of-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Jakie są podstawowe metody migracji?

Podstawowe metody migracji są następujące:

* Wykonaj lokalną kopię zapasową przy użyciu kompresji, a następnie ręcznie Skopiuj plik kopii zapasowej na maszynę wirtualną platformy Azure.
* Wykonaj kopię zapasową do adresu URL, a następnie Przywróć ją na maszynie wirtualnej platformy Azure przy użyciu adresu URL.
* Odłącz pliki danych i dziennika, skopiuj je do usługi Azure Blob Storage, a następnie dołącz je do SQL Server na maszynie wirtualnej platformy Azure przy użyciu adresu URL.
* Przekonwertuj lokalną maszynę fizyczną na dysk VHD funkcji Hyper-V, przekaż go do usługi Azure Blob Storage, a następnie wdróż ją jako nową maszynę wirtualną przy użyciu przekazanego wirtualnego dysku twardego.
* Wyślij dysk twardy za pomocą usługi Import/Export systemu Windows.
* Jeśli lokalne wdrożenie grupy dostępności jest włączone, użyj [Kreatora dodawania repliki platformy Azure](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) , aby utworzyć replikę na platformie Azure, w trybie failover i wskazać użytkowników do wystąpienia usługi Azure Database.
* Użyj SQL Server [replikacji transakcyjnej](/sql/relational-databases/replication/transactional/transactional-replication) , aby skonfigurować wystąpienie usługi Azure SQL Server jako subskrybent, wyłącz replikację i wskazywać użytkownikom wystąpienie usługi Azure Database.

> [!TIP]
> Możesz również użyć tych samych technik do przenoszenia baz danych między maszynami wirtualnymi SQL Server na platformie Azure. Na przykład nie ma żadnego obsługiwanego sposobu uaktualnienia SQL Server Galeria obrazów z jednej wersji/wydania do innej. W takim przypadku należy utworzyć nową maszynę wirtualną SQL Server z nową wersją/wersją, a następnie użyć jednej z technik migracji w tym artykule, aby przenieść bazy danych. 

## <a name="choose-a-migration-method"></a>Wybierz metodę migracji

Aby uzyskać najlepszą wydajność transferu danych, dokonaj migracji plików bazy danych na maszynę wirtualną platformy Azure przy użyciu skompresowanego pliku kopii zapasowej.

Aby zminimalizować przestoje podczas procesu migracji bazy danych, należy użyć opcji AlwaysOn lub opcji replikacja transakcyjna.

Jeśli nie można użyć powyższych metod, należy ręcznie przeprowadzić migrację bazy danych. Ogólnie rzecz biorąc, należy zacząć od kopii zapasowej bazy danych, wykonać ją z kopią kopii zapasowej bazy danych na platformie Azure, a następnie przywrócić bazę danych. Możesz również skopiować pliki bazy danych na platformę Azure, a następnie dołączyć je. Istnieje kilka metod, za pomocą których można wykonać ten ręczny proces migrowania bazy danych na maszynę wirtualną platformy Azure.

> [!NOTE]
> Po uaktualnieniu do SQL Server 2014 lub SQL Server 2016 ze starszych wersji SQL Server należy wziąć pod uwagę, czy zmiany są potrzebne. Zaleca się, aby zająć się wszystkimi zależnościami od funkcji nieobsługiwanych przez nową wersję SQL Server w ramach projektu migracji. Aby uzyskać więcej informacji na temat obsługiwanych wersji i scenariuszy, zobacz [uaktualnianie do SQL Server](/sql/database-engine/install-windows/upgrade-sql-server).

W poniższej tabeli wymieniono wszystkie podstawowe metody migracji i omówiono, kiedy użycie każdej metody jest najbardziej odpowiednie.

| Metoda | Wersja źródłowej bazy danych | Wersja docelowej bazy danych | Ograniczenie rozmiaru kopii zapasowej źródłowej bazy danych | Uwagi |
| --- | --- | --- | --- | --- |
| [Wykonaj lokalną kopię zapasową przy użyciu kompresji i ręcznie Skopiuj plik kopii zapasowej do maszyny wirtualnej platformy Azure](#back-up-and-restore) |SQL Server 2005 lub więcej |SQL Server 2005 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) | Ta technika jest prosta i dobrze sprawdzona pod kątem przemieszczania baz danych między maszynami. |
| [Wykonaj kopię zapasową do adresu URL i przywróć ją na maszynie wirtualnej platformy Azure przy użyciu adresu URL](#backup-to-url-and-restore-from-url) |SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2 lub nowszym | SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2 lub nowszym | < 12,8 TB dla SQL Server 2016, w przeciwnym razie < 1 TB | Ta metoda jest już inna metodą przenoszenia pliku kopii zapasowej na maszynę wirtualną przy użyciu usługi Azure Storage. |
| [Odłącz i skopiuj pliki danych i dziennika do usługi Azure Blob Storage, a następnie Dołącz do SQL Server na maszynie wirtualnej platformy Azure z adresu URL](#detach-and-attach-from-a-url) | SQL Server 2005 lub więcej |SQL Server 2014 lub więcej | [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) | Ta metoda służy do [przechowywania tych plików przy użyciu usługi Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) i dołączania ich do SQL Server działających na maszynie wirtualnej platformy Azure, szczególnie w przypadku bardzo dużych baz danych |
| [Przekonwertuj maszynę lokalną na wirtualne dyski twarde funkcji Hyper-V, Przekaż do usługi Azure Blob Storage, a następnie wdróż nową maszynę wirtualną przy użyciu przekazanego wirtualnego dysku twardego](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 lub więcej |SQL Server 2005 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) |Użyj przy korzystaniu [z własnej licencji SQL Server](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)podczas migrowania bazy danych, która będzie uruchamiana w starszej wersji SQL Server, lub podczas migrowania baz danych systemu i użytkownika w ramach migracji bazy danych, zależnie od innych baz danych użytkownika i/lub systemowych baz danych. |
| [Wyślij dysk twardy przy użyciu usługi Windows Import/Export](#ship-a-hard-drive) |SQL Server 2005 lub więcej |SQL Server 2005 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) |Korzystanie z [usługi Import/Export systemu Windows](../../../import-export/storage-import-export-service.md) , gdy ręczna metoda kopiowania jest zbyt wolna, na przykład z bardzo dużymi bazami danych |
| [Korzystanie z Kreatora dodawania repliki platformy Azure](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 lub więcej |SQL Server 2012 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) |Minimalizuje czas przestoju, należy używać w przypadku, gdy istnieje zawsze lokalne wdrożenie |
| [Użyj SQL Server replikacji transakcyjnej](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 lub więcej |SQL Server 2005 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) |Używaj, gdy zachodzi potrzeba zminimalizowania przestoju i nie istnieje zawsze lokalne wdrożenie |

## <a name="back-up-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Wykonaj kopię zapasową bazy danych z kompresją, skopiuj kopię zapasową do maszyny wirtualnej, a następnie Przywróć bazę danych. Jeśli plik kopii zapasowej jest większy niż 1 TB, należy utworzyć zestaw rozłożony, ponieważ maksymalny rozmiar dysku maszyny wirtualnej wynosi 1 TB. Wykonaj następujące ogólne kroki, aby przeprowadzić migrację bazy danych użytkownika przy użyciu tej metody ręcznej:

1. Wykonaj pełną kopię zapasową bazy danych w lokalizacji lokalnej.
2. Utwórz lub Przekaż maszynę wirtualną z odpowiednią wersją SQL Server.
3. Skonfiguruj łączność na podstawie Twoich wymagań. Zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure (Menedżer zasobów)](ways-to-connect-to-sql.md).
4. Skopiuj pliki kopii zapasowej na maszynę wirtualną za pomocą pulpitu zdalnego, Eksploratora Windows lub polecenia copy z wiersza polecenia.

## <a name="backup-to-url-and-restore-from-url"></a>Utwórz kopię zapasową do adresu URL i Przywróć z adresu URL

Zamiast tworzyć kopie zapasowe do pliku lokalnego, można użyć funkcji [tworzenia kopii zapasowej do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) , a następnie przywrócić z adresu URL do maszyny wirtualnej. SQL Server 2016 obsługuje zestawy kopii zapasowych. Są one zalecane do wydajności i są wymagane, aby przekroczyć limity rozmiaru na obiekt BLOB. W przypadku bardzo dużych baz danych zalecane jest korzystanie z [usługi Import/Export systemu Windows](../../../import-export/storage-import-export-service.md) .

## <a name="detach-and-attach-from-a-url"></a>Odłączanie i dołączanie adresu URL

Odłącz bazę danych i pliki dziennika, a następnie prześlij je do [usługi Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). Następnie Dołącz bazę danych z adresu URL na maszynie wirtualnej platformy Azure. Użyj tej metody, jeśli chcesz, aby pliki fizycznych baz danych znajdowały się w magazynie obiektów blob, co może być przydatne w przypadku bardzo dużych baz danych. Wykonaj następujące ogólne kroki, aby przeprowadzić migrację bazy danych użytkownika przy użyciu tej metody ręcznej:

1. Odłączanie plików bazy danych od lokalnego wystąpienia bazy danych.
2. Skopiuj odłączone pliki bazy danych do usługi Azure Blob Storage za pomocą [narzędzia wiersza polecenia AzCopy](../../../storage/common/storage-use-azcopy-v10.md).
3. Dołącz pliki bazy danych z adresu URL platformy Azure do wystąpienia SQL Server na maszynie wirtualnej platformy Azure.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>Konwertuj na maszynę wirtualną, Przekaż do adresu URL i Wdróż ją jako nową maszynę wirtualną

Ta metoda służy do migrowania wszystkich baz danych systemu i użytkownika w lokalnym wystąpieniu SQL Server na maszynę wirtualną platformy Azure. Wykonaj następujące ogólne kroki, aby przeprowadzić migrację całego wystąpienia SQL Server przy użyciu tej metody ręcznej:

1. Konwertowanie maszyn fizycznych lub wirtualnych na wirtualne dyski twarde funkcji Hyper-V.
2. Przekaż pliki VHD do usługi Azure Storage za pomocą [polecenia cmdlet Add-AzureVHD](/previous-versions/azure/dn495173(v=azure.100)).
3. Wdróż nową maszynę wirtualną przy użyciu przekazanego wirtualnego dysku twardego.

> [!NOTE]
> Aby przeprowadzić migrację całej aplikacji, należy rozważyć użycie [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-a-hard-drive"></a>Dostarcz dysk twardy

Korzystając z [metody usługi Import/Export systemu Windows](../../../import-export/storage-import-export-service.md) , można transferować duże ilości danych plików do usługi Azure Blob Storage w sytuacjach, gdy przekazywanie przez sieć jest niezwykle kosztowne lub niemożliwe. Za pomocą tej usługi wysyłasz co najmniej jeden dysk twardy zawierający te dane do centrum danych platformy Azure, w którym dane zostaną przekazane do konta magazynu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [SQL Server na platformie Azure — omówienie Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).

Aby uzyskać instrukcje dotyczące tworzenia SQL Server na maszynie wirtualnej platformy Azure z przechwyconego obrazu, zobacz [porady & wskazówki dotyczące "klonowania" maszyn wirtualnych usługi Azure SQL z przechwyconych obrazów](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images) w blogu SQL Server inżynierów CSS.