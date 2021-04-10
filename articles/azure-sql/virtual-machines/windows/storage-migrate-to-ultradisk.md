---
title: Migruj dysk dziennika do programu Ultra Disk
description: Dowiedz się, jak migrować SQL Server na dysku dziennika maszyny wirtualnej platformy Azure do usługi Azure Ultradisk, aby korzystać z wysokiej wydajności i małych opóźnień.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e8410b4e0997798eba5ee91f361c3a5f1ce47ef1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586305"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>Migruj dysk dziennika do programu Ultra Disk
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Usługa Azure Ultra disks zapewnia wysoką przepływność, dużą liczbę operacji we/wy na sekundę i ciągle małą ilość miejsca na dysku na potrzeby SQL Server na maszynie wirtualnej platformy Azure. 

W tym artykule przedstawiono sposób migrowania dysku dziennika do Ultra SSD, aby korzystać z zalet wydajności oferowanych przez Ultra Disks. 

## <a name="back-up-database"></a>Tworzenie kopii zapasowej bazy danych

Wykonaj [pełną kopię zapasową](backup-restore.md) bazy danych. 

## <a name="attach-disk"></a>Dołącz dysk

Dołącz SSD w warstwie Ultra do maszyny wirtualnej po włączeniu zgodności ultradisk na maszynie wirtualnej. 

Obudowa typu Ultra Disk jest obsługiwana w podzestawie rozmiarów i regionów maszyn wirtualnych. Przed kontynuowaniem sprawdź, czy maszyna wirtualna znajduje się w regionie, strefie i rozmiarze, który obsługuje Ultra Disk. Można [określić rozmiar i region maszyny wirtualnej](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

### <a name="enable-compatibility"></a>Włącz zgodność

Aby włączyć zgodność, wykonaj następujące kroki:

1. Przejdź do maszyny wirtualnej w [Azure Portal](https://portal.azure.com/). 
1. Zatrzymaj/Cofnij przydział maszyny wirtualnej. 
1. Wybierz pozycję **dyski** w obszarze **Ustawienia** , a następnie wybierz pozycję **dodatkowe ustawienia**. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Wybierz dodatkowe ustawienia dla dysków w obszarze Ustawienia w Azure Portal":::

1. Wybierz opcję **tak** , aby **włączyć zgodność Ultra Disk**. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Zrzut ekranu, na którym jest wyświetlana opcja tak.":::

1. Wybierz pozycję **Zapisz**. 



### <a name="attach-disk"></a>Dołącz dysk

Użyj Azure Portal, aby dołączyć dysk Ultra do maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [dołączanie Ultra Disk](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk).

Po dołączeniu dysku, uruchom maszynę wirtualną za pomocą Azure Portal. 



## <a name="format-disk"></a>Formatuj dysk

Nawiąż połączenie z maszyną wirtualną i sformatuj dysk.  

Aby sformatować dysk Ultra, wykonaj następujące kroki:

1. Połącz się z maszyną wirtualną przy użyciu Remote Desktop Protocol (RDP).
1. Użyj [przystawki Zarządzanie dyskami](/windows-server/storage/disk-management/overview-of-disk-management) , aby sformatować i podzielić nowo dołączoną dyskietkę. 


## <a name="use-disk-for-log"></a>Użyj dysku do dziennika

Skonfiguruj SQL Server tak, aby korzystał z nowego dysku dziennika. Można to zrobić przy użyciu języka Transact-SQL (T-SQL) lub SQL Server Management Studio (SSMS). Konto używane na potrzeby konta usługi SQL Server musi mieć pełną kontrolę nad nową lokalizacją w pliku dziennika. 

### <a name="configure-permissions"></a>Konfigurowanie uprawnień

1. Sprawdź konto usługi używane przez SQL Server. Można to zrobić za pomocą SQL Server Configuration Manager lub Services. msc.
1. Przejdź do nowego dysku. 
1. Utwórz folder (lub wiele folderów) do użycia w pliku dziennika. 
1. Kliknij prawym przyciskiem myszy folder i wybierz polecenie **Właściwości**.
1. Na karcie **zabezpieczenia** Udziel dostępu Pełna kontrola do konta usługi SQL Server. 
1. Wybierz **przycisk OK**  , aby zapisać ustawienia. 
1. Powtórz tę czynność dla każdego folderu głównego poziomu, w którym planujesz mieć dane SQL. 

### <a name="use-new-log-drive"></a>Użyj nowego dysku dziennika 

Po udzieleniu uprawnień Użyj języka Transact-SQL (T-SQL) lub SQL Server Management Studio (SSMS), aby odłączyć bazę danych i przenieść istniejące pliki dziennika do nowej lokalizacji.

   > [!CAUTION]
   > Odłączenie bazy danych spowoduje jej przejście w tryb offline, zamknięcie połączeń i wycofanie wszystkich transakcji, które są w locie. Kontynuuj zachowanie ostrożności i w czasie trwania okna obsługi. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

Aby przenieść istniejące pliki do nowej lokalizacji, Użyj języka T-SQL:

1. Nawiąż połączenie z bazą danych w SQL Server Management Studio i Otwórz **nowe okno zapytania** . 
1. Pobierz istniejące pliki i lokalizacje:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. Odłącz bazę danych: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. Aby przenieść plik dziennika do nowej lokalizacji na dysku Ultra, użyj Eksploratora plików. 

1. Dołącz bazę danych, określając nowe lokalizacje plików: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

W tym momencie baza danych przejdzie w tryb online z dziennikiem w nowej lokalizacji. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

Przenieś istniejące pliki do nowej lokalizacji za pomocą programu SSMS:

1. Nawiąż połączenie z bazą danych w SQL Server Management Studio (SSMS). 
1. Kliknij prawym przyciskiem myszy bazę danych, wybierz pozycję **Właściwości** , a następnie wybierz pozycję **pliki**. 
1. Zanotuj ścieżkę istniejących plików. 
1. Wybierz przycisk **OK**, aby zamknąć okno dialogowe. 
1. Kliknij prawym przyciskiem myszy bazę danych, wybierz pozycję **zadania**  >  **Odłącz**. 
1. Postępuj zgodnie z instrukcjami kreatora, aby odłączyć bazę danych. 
1. Za pomocą Eksploratora plików ręcznie Przenieś plik dziennika do nowej lokalizacji.
1. Dołącz bazę danych w SQL Server Management Studio
   1. Kliknij prawym przyciskiem myszy pozycję **bazy danych** w **Eksplorator obiektów** a następnie wybierz polecenie **Dołącz bazę danych**. 
   1. Za pomocą okna dialogowego Dodaj każdy plik, w tym plik dziennika w nowej lokalizacji. 
   1. Wybierz **przycisk OK** , aby dołączyć bazę danych. 

W tym momencie baza danych przejdzie w tryb online z dziennikiem w nowej lokalizacji.

---


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [najlepszymi rozwiązaniami](performance-guidelines-best-practices.md) dotyczącymi wydajności, aby zwiększyć wydajność. 

Aby zapoznać się z omówieniem SQL Server w usłudze Azure Virtual Machines, zobacz następujące artykuły:

- [Omówienie SQL Server na maszynach wirtualnych z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Przegląd SQL Server on Linux maszyn wirtualnych](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
