---
title: Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure
description: W tym artykule wyjaśniono, jak wrócić do SQL Server na platformie Azure. W artykule objaśniono również proces odzyskiwania programu SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b6daf631248958948e799b20284d84a1e59e5dfe
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518868"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informacje o kopii zapasowej programu SQL Server na maszynach wirtualnych platformy Azure

[Azure Backup](backup-overview.md) oferuje oparte na strumieniu, wyspecjalizowane rozwiązanie do kopii zapasowej SQL Server działających na platformie Azure. To rozwiązanie jest zgodne Azure Backup zaletami tworzenia kopii zapasowych bez infrastruktury, długoterminowego przechowywania i centralnego zarządzania. Ponadto zapewnia on następujące korzyści specjalnie dla SQL Server:

1. Kopie zapasowe z obsługą obciążeń, które obsługują wszystkie typy kopii zapasowych — pełne, różnicowe i dziennika
2. 15-minutowy cel punktu odzyskiwania (recovery point objective) z częstymi kopiami zapasami dziennika
3. Odzyskiwanie do punktu w czasie do sekundy
4. Tworzenie kopii zapasowej i przywracanie na poziomie poszczególnych baz danych

Aby wyświetlić scenariusze tworzenia kopii zapasowych i przywracania, które obsługujemy obecnie, zapoznaj się z [macierzą obsługi](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

To rozwiązanie korzysta z natywnych interfejsów API SQL do tworzenia kopii zapasowych baz danych SQL.

* Po określeniu maszyny SQL Server wirtualnej, którą chcesz chronić, i zapytaniu o bazy danych, usługa Azure Backup zainstaluje rozszerzenie kopii zapasowej obciążenia na maszynie wirtualnej przez rozszerzenie `AzureBackupWindowsWorkload` nazwy.
* To rozszerzenie składa się z koordynatora i wtyczki SQL. Koordynator jest odpowiedzialny za wyzwalanie przepływów pracy dla różnych operacji, takich jak konfigurowanie kopii zapasowych, tworzenie kopii zapasowych i przywracanie, ale wtyczka jest odpowiedzialna za rzeczywisty przepływ danych.
* Aby móc odnajdywać bazy danych na tej maszynie wirtualnej, Azure Backup `NT SERVICE\AzureWLBackupPluginSvc` konto. To konto jest używane do tworzenia kopii zapasowych i przywracania oraz wymaga uprawnień administratora systemu SQL. To `NT SERVICE\AzureWLBackupPluginSvc` konto jest kontem usługi [wirtualnej,](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)dlatego nie wymaga zarządzania hasłami. Azure Backup używa konta do odnajdywania/zapytań bazy danych, dlatego to konto musi być publicznym identyfikatorem logowania w `NT AUTHORITY\SYSTEM` języku SQL. Jeśli nie utworzono maszyny wirtualnej programu SQL Server z witryny Azure Marketplace, może wystąpić błąd **UserErrorSQLNoSysadminMembership**. W takim przypadku [wykonaj te instrukcje](#set-vm-permissions).
* Po wyzwoleniu konfigurowania ochrony dla wybranych baz danych usługa tworzenia kopii zapasowych konfiguruje koordynatora przy użyciu harmonogramów tworzenia kopii zapasowych i innych szczegółów zasad, które rozszerzenie buforuje lokalnie na maszynie wirtualnej.
* W zaplanowanym czasie koordynator komunikuje się z wtyczką i rozpoczyna przesyłanie strumieniowe danych kopii zapasowej z serwera SQL przy użyciu VDI.  
* Wtyczka wysyła dane bezpośrednio do magazynu usługi Recovery Services, eliminując w ten sposób konieczność przechowywania lokalizacji przejściowej. Dane są szyfrowane i przechowywane przez usługę Azure Backup na kontach magazynu.
* Po zakończeniu transferu danych koordynator potwierdza zatwierdzenie za pomocą usługi tworzenia kopii zapasowej.

  ![Architektura usługi SQL Backup](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem sprawdź następujące wymagania:

1. Upewnij się, że masz wystąpienie programu SQL Server uruchomione na platformie Azure. Możesz [szybko utworzyć wystąpienie programu SQL Server](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) w witrynie Marketplace.
2. Zapoznaj się z [zagadnieniami na temat funkcji i](sql-support-matrix.md#feature-considerations-and-limitations) [obsługą scenariuszy.](sql-support-matrix.md#scenario-support)
3. [Przejrzyj często zadawane pytania](faq-backup-sql-server.yml) dotyczące tego scenariusza.

## <a name="set-vm-permissions"></a>Ustawianie uprawnień maszyny wirtualnej

  Po uruchomieniu odnajdywania na SQL Server Azure Backup następujące czynności:

* Dodaje rozszerzenie AzureBackupWindowsWorkload.
* Tworzy konto NT SERVICE\AzureWLBackupPluginSvc w celu odnajdywania baz danych na maszynie wirtualnej. To konto służy do tworzenia kopii zapasowych i przywracania oraz wymaga uprawnień administratora systemu SQL.
* Odnajduje bazy danych, które są uruchomione na maszynie wirtualnej, Azure Backup używa konta NT AUTHORITY\SYSTEM. To konto musi być publicznym logowaniem w języku SQL.

Jeśli nie utworzysz maszyny wirtualnej z programem SQL Server w programie Azure Marketplace lub korzystasz z programu SQL 2008 lub 2008 R2, może zostać wyświetlony błąd **UserErrorSQLNoSysadminMembership.**

Uprawnienia w przypadku programu **SQL 2008** i **2008 R2 działającego** w systemie Windows 2008 R2 można znaleźć [tutaj.](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)

W przypadku wszystkich innych wersji napraw uprawnienia, aby wykonać następujące czynności:

  1. Zaloguj się do programu SQL Server Management Studio (SSMS), używając konta z uprawnieniami administratora systemu SQL Server. Jeśli nie potrzebujesz specjalnych uprawnień, uwierzytelnianie systemu Windows powinno działać.
  2. Na SQL Server otwórz folder **Security/Logins.**

      ![Otwieranie folderu Security/Logins w celu wyświetlenia kont](./media/backup-azure-sql-database/security-login-list.png)

  3. Kliknij prawym przyciskiem myszy folder **Logins** i wybierz polecenie **New Login (Nowy identyfikator logowania).** W oknie **Nazwa logowania — nowa** wybierz pozycję **Wyszukaj**.

      ![Wybieranie pozycji Wyszukaj w oknie dialogowym Nazwa logowania — nowa](./media/backup-azure-sql-database/new-login-search.png)

  4. Konto usługi wirtualnej systemu Windows **NT SERVICE\AzureWLBackupPluginSvc** zostało utworzone podczas rejestracji maszyny wirtualnej i fazy odnajdywania SQL. Wprowadź nazwę konta, jak pokazano w polu **Wprowadź nazwę obiektu do wybrania**. Wybierz pozycję **Sprawdź nazwy** w celu rozpoznania nazwy. Wybierz przycisk **OK**.

      ![Wybieranie pozycji Sprawdź nazwy w celu rozpoznania nieznanej nazwy](./media/backup-azure-sql-database/check-name.png)

  5. W obszarze **Role serwera** sprawdź, czy wybrano rolę **sysadmin**. Wybierz przycisk **OK**. Wymagane uprawnienia powinny teraz istnieć.

      ![Sprawdzanie, czy wybrano rolę sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Skojarz bazę danych z magazynem usługi Recovery Services. W witrynie Azure Portal na liście **Serwery chronione** kliknij prawym przyciskiem myszy serwer w stanie błędu i wybierz pozycję **Ponownie znajdź bazy danych**.

      ![Sprawdzanie, czy serwer ma odpowiednie uprawnienia](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Sprawdź postęp w obszarze **Powiadomienia**. Po znalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

      ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Jeśli w SQL Server zainstalowano wiele wystąpień programu SQL Server, musisz dodać uprawnienie sysadmin dla konta **NT Service\AzureWLBackupPluginSvc** do wszystkich wystąpień SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Nadaj uprawnienia administratora systemu SQL dla programu SQL 2008 i SQL 2008 R2

Dodaj identyfikatory logowania NT **AUTHORITY\SYSTEM** i **NT Service\AzureWLBackupPluginSvc** do SQL Server Wystąpienia:

1. Przejdź do SQL Server w Eksploratorze obiektów.
2. Przejdź do strony Security -> Logins (Zabezpieczenia — > logowania).
3. Kliknij prawym przyciskiem myszy identyfikatory logowania i wybierz pozycję *Nowy identyfikator logowania...*

    ![Nowe logowanie przy użyciu programu SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Przejdź do karty Ogólne i wprowadź **NT AUTHORITY\SYSTEM** jako nazwę logowania.

    ![Nazwa logowania dla SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Przejdź do *pozycji Role serwera i* wybierz role *publiczne* *i sysadmin.*

    ![Wybieranie ról w programie SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Przejdź do *stanu*. *Przyznaj* uprawnienia do nawiązywania połączenia z aparatem bazy danych i zaloguj się jako *Włączone.*

    ![Udzielanie uprawnień w programie SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Wybierz przycisk OK.
8. Powtórz tę samą sekwencję kroków (1–7 powyżej), aby dodać identyfikator logowania NT Service\AzureWLBackupPluginSvc do SQL Server wystąpienia. Jeśli identyfikator logowania już istnieje, upewnij się, że ma rolę serwera sysadmin i w obszarze Stan ma uprawnienia do nawiązywania połączenia z aparatem bazy danych i Zaloguj się jako Włączone.
9. Po udzieleniu uprawnień **odszukaj bazy danych** ponownie w portalu: Obciążenie infrastruktury kopii zapasowej magazynu na maszynie **->** **->** wirtualnej platformy Azure:

    ![Ponowne wykrywania kb/s w Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Alternatywnie możesz zautomatyzować udzielanie uprawnień, uruchamiając następujące polecenia programu PowerShell w trybie administratora. Nazwa wystąpienia jest domyślnie ustawiona na MSSQLSERVER. W razie potrzeby zmień argument nazwy wystąpienia w skrypcie:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o](backup-sql-server-database-azure-vms.md) kopii zapasowej SQL Server baz danych.
* [Dowiedz się więcej](restore-sql-database-azure-vm.md) o przywracaniu baz danych programu SQL Server z kopii zapasowych.
* [Dowiedz się więcej](manage-monitor-sql-database-backup.md) o zarządzaniu bazami danych programu SQL Server, dla których są tworzone kopie zapasowe.
