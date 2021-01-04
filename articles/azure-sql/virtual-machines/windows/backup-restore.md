---
title: Tworzenie kopii zapasowych i przywracanie SQL Server na maszynach wirtualnych platformy Azure | Microsoft Docs
description: Opisuje zagadnienia dotyczące tworzenia kopii zapasowych i przywracania SQL Server baz danych uruchomionych w usłudze Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: 2fcba81bcd20db321d791fcda589f40fb0699702
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733077"
---
# <a name="backup-and-restore-for-sql-server-on-azure-vms"></a>Tworzenie kopii zapasowych i przywracanie SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ten artykuł zawiera wskazówki dotyczące opcji tworzenia kopii zapasowych i przywracania dostępnych dla SQL Server uruchomionych na maszynie wirtualnej z systemem Windows na platformie Azure. Usługa Azure Storage przechowuje trzy kopie każdego dysku maszyny wirtualnej platformy Azure w celu zagwarantowania ochrony przed utratą danych lub uszkodzeniem danych fizycznych. Tak więc, w przeciwieństwie do SQL Server lokalnych, nie trzeba skupić się na awariach sprzętu. Należy jednak nadal tworzyć kopie zapasowe baz danych SQL Server, aby chronić je przed błędami aplikacji lub użytkowników, na przykład nieumyślne wstawienia lub usunięcia danych. W takiej sytuacji ważne jest, aby można było przywrócić do określonego punktu w czasie.

Pierwsza część tego artykułu zawiera omówienie dostępnych opcji tworzenia kopii zapasowych i przywracania. Poniżej znajdują się sekcje, które zawierają więcej informacji na temat każdej strategii.

## <a name="backup-and-restore-options"></a>Opcje tworzenia kopii zapasowej i przywracania

Poniższa tabela zawiera informacje dotyczące różnych opcji tworzenia kopii zapasowych i przywracania SQL Server na maszynach wirtualnych platformy Azure:

| Strategia | Wersje SQL | Opis |
|---|---|---|
| [Automatyczne usługa Backup](#automated) | 2014<br/> 2016<br/> 2017 | Automatyczne tworzenie kopii zapasowych umożliwia planowanie regularnych kopii zapasowych dla wszystkich baz danych na maszynie wirtualnej SQL Server. Kopie zapasowe są przechowywane w usłudze Azure Storage przez maksymalnie 30 dni. Począwszy od SQL Server 2016, automatyczna kopia zapasowa v2 oferuje dodatkowe opcje, takie jak Konfigurowanie harmonogramu ręcznego oraz częstotliwość pełnych i dzienników kopii zapasowych. |
| [Usługa Azure Backup dla maszyn wirtualnych SQL](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017<br/> 2019 | Azure Backup zapewnia funkcję tworzenia kopii zapasowych klasy korporacyjnej dla SQL Server na maszynach wirtualnych platformy Azure. Za pomocą tej usługi można centralnie zarządzać kopiami zapasowymi dla wielu serwerów i tysięcy baz danych. Bazy danych można przywrócić do określonego punktu w czasie w portalu. Oferuje to dostosowywalne zasady przechowywania, które mogą obsługiwać kopie zapasowe przez lata. |
| [Ręczna kopia zapasowa](#manual) | Wszystko | W zależności od używanej wersji SQL Server istnieją różne techniki ręcznego tworzenia kopii zapasowych i przywracania SQL Server na maszynie wirtualnej platformy Azure. W tym scenariuszu użytkownik jest odpowiedzialny za tworzenie kopii zapasowych baz danych oraz lokalizację magazynu oraz zarządzanie tymi kopiami zapasowymi. |

W poniższych sekcjach opisano każdą opcję bardziej szczegółowo. Ostatnia sekcja tego artykułu zawiera podsumowanie w formie macierzy funkcji.

## <a name="automated-backup"></a><a id="automated"></a> Automatyczna kopia zapasowa

Automatyczne kopie zapasowe zapewniają usługę automatycznego tworzenia kopii zapasowych dla SQL Server Standard i wersji Enterprise działających na maszynie wirtualnej z systemem Windows na platformie Azure. Ta usługa jest świadczona przez [SQL Server rozszerzenie agenta IaaS](sql-server-iaas-agent-extension-automate-management.md), które jest instalowane automatycznie na SQL Server obrazów maszyn wirtualnych z systemem Windows w Azure Portal.

Kopie zapasowe wszystkich baz danych są tworzone na konto usługi Azure Storage, które można skonfigurować. Kopie zapasowe mogą być szyfrowane i przechowywane przez maksymalnie 30 dni.

SQL Server 2016 i nowsze maszyny wirtualne oferują więcej opcji dostosowywania dla zautomatyzowanych kopii zapasowych v2. Te ulepszenia są następujące:

- Kopie zapasowe systemowej bazy danych
- Harmonogram ręcznego tworzenia kopii zapasowych i przedział czasu
- Częstotliwość tworzenia kopii zapasowych pełnych i plików dziennika

Aby przywrócić bazę danych, należy zlokalizować wymagane pliki kopii zapasowej na koncie magazynu i przeprowadzić przywracanie na maszynie wirtualnej SQL przy użyciu poleceń SQL Server Management Studio (SSMS) lub Transact-SQL.

Aby uzyskać więcej informacji na temat konfigurowania zautomatyzowanej kopii zapasowej dla maszyn wirtualnych SQL, zobacz jeden z następujących artykułów:

- **SQL Server 2016/2017**: [Automatyczne kopie zapasowe v2 dla platformy Azure Virtual Machines](automated-backup.md)
- **SQL Server 2014**: [Automatyczne kopie zapasowe dla SQL Server 2014 Virtual Machines](automated-backup-sql-2014.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a> Azure Backup dla maszyn wirtualnych SQL

[Azure Backup](../../../backup/index.yml) zapewnia funkcję tworzenia kopii zapasowych klasy korporacyjnej dla SQL Server na maszynach wirtualnych platformy Azure. Wszystkie kopie zapasowe są przechowywane i zarządzane w magazynie Recovery Services. Istnieje kilka korzyści, które zapewnia to rozwiązanie, szczególnie w przypadku przedsiębiorstw:

- **Tworzenie kopii zapasowej bez infrastruktury**: nie ma potrzeby zarządzania serwerami kopii zapasowych ani lokalizacjami magazynu.
- **Skalowanie**: Ochrona wielu maszyn wirtualnych SQL i tysięcy baz danych.
- **Płatność zgodnie z rzeczywistym** użyciem: Ta funkcja jest oddzielną usługą oferowaną przez Azure Backup, ale podobnie jak w przypadku wszystkich usług platformy Azure, płacisz tylko za to, czego używasz.
- **Centralne zarządzanie i monitorowanie**: centralne zarządzanie wszystkimi kopiami zapasowymi, w tym innymi obciążeniami obsługiwanymi przez Azure Backup, z jednego pulpitu nawigacyjnego na platformie Azure.
- Tworzenie **kopii zapasowych i przechowywanie** na podstawie zasad: Utwórz standardowe zasady tworzenia kopii zapasowych dla zwykłych kopii zapasowych. Ustanów zasady przechowywania do obsługi kopii zapasowych przez lata.
- **Obsługa funkcji SQL Always On**: wykrywanie i ochrona SQL Server zawsze włączona konfiguracja i przestrzeganie preferencji tworzenia kopii zapasowej grupy dostępności.
- **15-minutowy cel punktu odzyskiwania**: Skonfiguruj kopie zapasowe dziennika transakcji SQL do co 15 minut.
- **Przywracanie do punktu w czasie**: Użyj portalu, aby odzyskać bazy danych do określonego punktu w czasie bez konieczności ręcznego przywracania wielu kopii zapasowych, różnicowych i dzienników.
- **Skonsolidowane alerty e-mail dotyczące niepowodzeń**: Skonfiguruj skonsolidowane powiadomienia e-mail pod kątem błędów.
- **Kontrola dostępu oparta na rolach na platformie Azure**: Określanie, kto może zarządzać operacjami tworzenia kopii zapasowych i przywracania za pośrednictwem portalu.

Aby zapoznać się z krótkim omówieniem, jak to działa wraz z pokazem, Obejrzyj następujące wideo:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

To Azure Backup rozwiązanie dla maszyn wirtualnych SQL jest ogólnie dostępne. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej bazy danych SQL Server Database na platformie Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a> Ręczna kopia zapasowa

Jeśli chcesz ręcznie zarządzać operacjami tworzenia kopii zapasowych i przywracania na maszynach wirtualnych SQL, istnieje kilka opcji w zależności od używanej wersji programu SQL Server. Aby zapoznać się z omówieniem tworzenia kopii zapasowych i przywracania, zobacz jeden z następujących artykułów w zależności od używanej wersji programu SQL Server:

- [Tworzenie kopii zapasowych i przywracanie dla SQL Server 2016 i nowszych](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Tworzenie kopii zapasowych i przywracanie dla SQL Server 2014](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases?viewFallbackFrom=sql-server-2014)
- [Tworzenie kopii zapasowych i przywracanie dla SQL Server 2012](/previous-versions/sql/sql-server-2012/ms187048(v=sql.110))
- [Tworzenie kopii zapasowych i przywracanie dla SQL Server SQL Server 2008 R2](/previous-versions/sql/sql-server-2008-r2/ms187048(v=sql.105))
- [Tworzenie kopii zapasowych i przywracanie dla SQL Server 2008](/previous-versions/sql/sql-server-2008/ms187048(v=sql.100))

W poniższych sekcjach szczegółowo opisano kilka opcji ręcznego tworzenia kopii zapasowych i przywracania.

### <a name="backup-to-attached-disks"></a>Tworzenie kopii zapasowych na podłączonych dyskach

Aby uzyskać SQL Server na maszynach wirtualnych platformy Azure, można użyć natywnych technik tworzenia kopii zapasowych i przywracania przy użyciu dołączonych dysków na maszynie wirtualnej w celu utworzenia plików kopii zapasowej. Istnieje jednak ograniczenie liczby dysków, które można dołączyć do maszyny wirtualnej platformy Azure, na podstawie [rozmiaru maszyny wirtualnej](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Należy również wziąć pod uwagę koszty zarządzania dyskami.

Przykład ręcznego tworzenia pełnej kopii zapasowej bazy danych przy użyciu programu SQL Server Management Studio (SSMS) lub Transact-SQL można znaleźć w temacie [Tworzenie pełnej kopii zapasowej bazy danych](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Utwórz kopię zapasową do adresu URL

Począwszy od SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2 można tworzyć kopie zapasowe i przywracać bezpośrednio do Microsoft Azure magazynu obiektów blob, co jest również znane jako kopia zapasowa do adresu URL. W SQL Server 2016 wprowadzono również następujące ulepszenia dla tej funkcji:

| udoskonalenie 2016 | Szczegóły |
| --- | --- |
| **Rozkładanie** |Podczas tworzenia kopii zapasowych do Microsoft Azure Blob Storage SQL Server 2016 obsługuje tworzenie kopii zapasowych do wielu obiektów BLOB w celu umożliwienia tworzenia kopii zapasowych dużych baz danych, maksymalnie 12,8 TB. |
| **Kopia zapasowa migawek** |Korzystając z migawek platformy Azure, SQL Server File-Snapshot kopia zapasowa oferuje niemal chwilowe kopie zapasowe i szybkie przywracanie plików bazy danych przechowywanych przy użyciu usługi Azure Blob Storage. Ta funkcja umożliwia uproszczenie zasad tworzenia kopii zapasowych i przywracania. Kopia zapasowa plików migawek obsługuje również przywracanie do punktu w czasie. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych migawek dla plików bazy danych na platformie Azure](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Aby uzyskać więcej informacji, zobacz jeden z następujących artykułów w zależności od używanej wersji programu SQL Server:

- **SQL Server 2016/2017**: [SQL Server tworzenia kopii zapasowej do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 Utwórz kopię zapasową do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service?viewFallbackFrom=sql-server-2014)
- **SQL Server 2012**: [SQL Server 2012 Utwórz kopię zapasową do adresu URL](/previous-versions/sql/sql-server-2012/jj919148(v=sql.110))

### <a name="managed-backup"></a>Tworzenie zarządzanej kopii zapasowej

Począwszy od SQL Server 2014, zarządzana kopia zapasowa automatyzuje tworzenie kopii zapasowych w usłudze Azure Storage. W tle Zarządzana kopia zapasowa korzysta z funkcji Backup to URL opisanej w poprzedniej sekcji tego artykułu. Zarządzana kopia zapasowa jest również podstawową funkcją, która obsługuje usługę automatycznego tworzenia kopii zapasowych maszyny wirtualnej SQL Server.

Począwszy od SQL Server 2016, zarządzana kopia zapasowa ma dodatkowe opcje planowania, tworzenia kopii zapasowej bazy danych i pełnych i dzienników.

Aby uzyskać więcej informacji, zobacz jeden z następujących artykułów w zależności od używanej wersji programu SQL Server:

- [Zarządzana kopia zapasowa do Microsoft Azure dla SQL Server 2016 i nowszych](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Zarządzana kopia zapasowa do Microsoft Azure dla SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?viewFallbackFrom=sql-server-2014)

## <a name="decision-matrix"></a>Macierz decyzji

Poniższa tabela zawiera podsumowanie możliwości poszczególnych opcji tworzenia kopii zapasowych i przywracania dla SQL Server maszyn wirtualnych na platformie Azure.

| Opcja | Automatyczne usługa Backup | Azure Backup dla SQL | Ręczna kopia zapasowa |
|---|---|---|---|
| Wymaga dodatkowej usługi platformy Azure |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Konfigurowanie zasad tworzenia kopii zapasowych w Azure Portal | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Przywracanie baz danych w Azure Portal |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Zarządzanie wieloma serwerami na jednym pulpicie nawigacyjnym |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Przywracanie do określonego momentu | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |
| 15-minutowy cel punktu odzyskiwania (RPO) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |
| Krótkoterminowe zasady przechowywania kopii zapasowych (dni) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Zasady długoterminowego przechowywania kopii zapasowych (miesiące, lata) |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Wbudowana obsługa SQL Server zawsze włączona |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Tworzenie kopii zapasowej na kontach usługi Azure Storage | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png)Automatyczna | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png)Automatyczna | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png)(zarządzane przez klienta) |
| Zarządzanie plikami magazynu i kopii zapasowych | | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |  |
| Tworzenie kopii zapasowych na dyskach dołączonych na maszynie wirtualnej |   |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |
| Centralne dostosowywalne raporty kopii zapasowych |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Skonsolidowane alerty e-mail dotyczące niepowodzeń |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Dostosowywanie monitorowania na podstawie dzienników Azure Monitor |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   |
| Monitoruj zadania tworzenia kopii zapasowej za pomocą programu SSMS lub skryptów języka Transact-SQL | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |
| Przywracanie baz danych za pomocą programu SSMS lub skryptów języka Transact-SQL | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |   | ![Zielony znacznik wyboru.](./media/backup-restore/yes.png) |

## <a name="next-steps"></a>Następne kroki

Jeśli planujesz wdrożenie SQL Server na maszynie wirtualnej platformy Azure, możesz znaleźć wskazówki dotyczące aprowizacji w następującym przewodniku: [jak zainicjować obsługę administracyjną maszyny wirtualnej z systemem Windows SQL Server w Azure Portal](create-sql-vm-portal.md).

Podczas migrowania danych przy użyciu funkcji tworzenia kopii zapasowych i przywracania można łatwiej SQL Server ścieżki migracji danych na maszynie wirtualnej. Pełne Omówienie opcji migracji i zaleceń znajduje się w temacie [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](migrate-to-vm-from-sql-server.md).
