---
title: Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure
description: W tym artykule opisano sposób tworzenia kopii zapasowych SQL Server na platformie Azure. W artykule objaśniono również proces odzyskiwania programu SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 510d9637031928e31abaa5f82a5bf58c6ef44719
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316849"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informacje o kopii zapasowej programu SQL Server na maszynach wirtualnych platformy Azure

[Azure Backup](backup-overview.md) oferuje oparty na strumieniu wyspecjalizowane rozwiązanie do tworzenia kopii zapasowych SQL Server uruchomionych na maszynach wirtualnych platformy Azure. To rozwiązanie jest dostosowane do Azure Backup korzyści z tworzenia kopii zapasowych bez infrastruktury, długoterminowego przechowywania i centralnego zarządzania. Ponadto zapewnia następujące korzyści dotyczące SQL Server:

1. Kopie zapasowe oparte na obciążeniu obsługujące wszystkie typy kopii zapasowych — pełne, różnicowe i dzienników
2. 15-minutowy cel punktu odzyskiwania z częstymi kopiami zapasowymi dzienników
3. Odzyskiwanie do punktu w czasie do drugiego
4. Kopia zapasowa i przywracanie na poziomie poszczególnych baz danych

Aby wyświetlić scenariusze tworzenia kopii zapasowych i przywracania, które obsługuje dzisiaj, zapoznaj się z [matrycą pomocy technicznej](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

To rozwiązanie wykorzystuje natywne interfejsy API SQL do wykonywania kopii zapasowych baz danych SQL.

* Po określeniu maszyny wirtualnej SQL Server, która ma być chroniona, i zapytania dotyczącej baz danych, usługa Azure Backup Service zainstaluje rozszerzenie kopii zapasowej obciążenia na maszynie wirtualnej o `AzureBackupWindowsWorkload` rozszerzeniu nazwy.
* To rozszerzenie składa się z koordynatora i wtyczki SQL. Chociaż koordynator jest odpowiedzialny za wyzwalanie przepływów pracy dla różnych operacji, takich jak konfigurowanie kopii zapasowej, tworzenie kopii zapasowej i przywracanie, wtyczka jest odpowiedzialna za rzeczywisty przepływ danych.
* Aby móc odnajdywać bazy danych na tej maszynie wirtualnej, Azure Backup tworzy konto `NT SERVICE\AzureWLBackupPluginSvc` . To konto jest używane na potrzeby tworzenia kopii zapasowych i przywracania oraz wymaga uprawnień administratora systemu SQL. `NT SERVICE\AzureWLBackupPluginSvc`Konto jest [kontem usługi wirtualnej](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)i nie wymaga zarządzania hasłami. Azure Backup używa `NT AUTHORITY\SYSTEM` konta do odnajdywania/wyszukiwania bazy danych, więc to konto musi być publicznym logowaniem na serwerze SQL. Jeśli nie utworzono maszyny wirtualnej programu SQL Server z witryny Azure Marketplace, może wystąpić błąd **UserErrorSQLNoSysadminMembership**. W takim przypadku [wykonaj te instrukcje](#set-vm-permissions).
* Po zainicjowaniu konfigurowania ochrony dla wybranych baz danych usługa tworzenia kopii zapasowych konfiguruje koordynatora przy użyciu harmonogramów tworzenia kopii zapasowych i innych szczegółów zasad, które są buforowane lokalnie na maszynie wirtualnej.
* W zaplanowanym czasie koordynator komunikuje się z wtyczką i zaczyna przesyłać strumieniowo dane kopii zapasowej z programu SQL Server przy użyciu infrastruktury VDI.  
* Wtyczka wysyła dane bezpośrednio do magazynu Recovery Services, co eliminuje konieczność lokalizacji tymczasowej. Dane są szyfrowane i przechowywane przez usługę Azure Backup na kontach magazynu.
* Po zakończeniu transferu danych koordynator potwierdza zatwierdzenie w usłudze kopii zapasowej.

  ![Architektura kopii zapasowych SQL](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

1. Upewnij się, że masz wystąpienie programu SQL Server uruchomione na platformie Azure. Możesz [szybko utworzyć wystąpienie programu SQL Server](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) w witrynie Marketplace.
2. Zapoznaj się z [zagadnieniami](sql-support-matrix.md#feature-considerations-and-limitations) [dotyczącymi funkcji i scenariuszami](sql-support-matrix.md#scenario-support).
3. [Przejrzyj często zadawane pytania](faq-backup-sql-server.md) dotyczące tego scenariusza.

## <a name="set-vm-permissions"></a>Ustawianie uprawnień maszyny wirtualnej

  Po uruchomieniu odnajdywania na SQL Server, Azure Backup wykonuje następujące czynności:

* Dodaje rozszerzenie AzureBackupWindowsWorkload.
* Tworzy konto NT SERVICE\AzureWLBackupPluginSvc do odnajdywania baz danych na maszynie wirtualnej. To konto jest używane do tworzenia kopii zapasowych i przywracania oraz wymaga uprawnień administratora systemu SQL.
* Odnajduje bazy danych, które są uruchomione na maszynie wirtualnej, Azure Backup korzysta z konta NT NT\SYSTEM. To konto musi być publicznym logowaniem na serwerze SQL.

Jeśli maszyna wirtualna SQL Server nie została utworzona w witrynie Azure Marketplace lub w przypadku korzystania z programu SQL 2008 lub 2008 R2, może wystąpić błąd **UserErrorSQLNoSysadminMembership** .

W przypadku nadawania uprawnień w przypadku **programów SQL 2008** i **2008 R2** uruchomionych w systemie Windows 2008 R2 zapoznaj się z [tym](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)artykułem.

W przypadku wszystkich innych wersji należy rozwiązać uprawnienia, wykonując następujące czynności:

  1. Zaloguj się do programu SQL Server Management Studio (SSMS), używając konta z uprawnieniami administratora systemu SQL Server. Jeśli nie potrzebujesz specjalnych uprawnień, uwierzytelnianie systemu Windows powinno działać.
  2. Na SQL Server otwórz folder **Security/Logins** .

      ![Otwieranie folderu Security/Logins w celu wyświetlenia kont](./media/backup-azure-sql-database/security-login-list.png)

  3. Kliknij prawym przyciskiem myszy folder **logowania** i wybierz pozycję **Nowa nazwa logowania**. W oknie **Nazwa logowania — nowa** wybierz pozycję **Wyszukaj**.

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
> Jeśli SQL Server ma zainstalowanych wiele wystąpień SQL Server, należy dodać uprawnienia sysadmin dla konta **NT Service\AzureWLBackupPluginSvc** do wszystkich wystąpień programu SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Nadaj uprawnienia administratora systemu SQL dla programu SQL 2008 i programu SQL 2008 R2

Dodaj logowania **NT NT\SYSTEM** i **NT Service\AzureWLBackupPluginSvc** do wystąpienia SQL Server:

1. Przejdź do wystąpienia SQL Server w Eksploratorze obiektów.
2. Przejdź do > logowania do zabezpieczeń
3. Kliknij prawym przyciskiem myszy nazwę logowania i wybierz pozycję *Nowa nazwa logowania...*

    ![Nowa nazwa logowania przy użyciu programu SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Przejdź do karty Ogólne i wprowadź **NT NT\SYSTEM** jako nazwę logowania.

    ![Nazwa logowania dla programu SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Przejdź do *ról serwera* i wybierz role *publiczne* i *sysadmin* .

    ![Wybieranie ról w programie SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Przejdź do pozycji *stan*. *Przyznaj* uprawnienie do nawiązywania połączenia z aparatem bazy danych i logowanie jako *włączone*.

    ![Przyznawanie uprawnień w programie SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Wybierz przycisk OK.
8. Powtórz tę samą sekwencję kroków (1-7 powyżej), aby dodać logowanie NT Service\AzureWLBackupPluginSvc do wystąpienia SQL Server. Jeśli logowanie już istnieje, upewnij się, że ma ona rolę serwera sysadmin i w obszarze stan, przyznaje uprawnienia do nawiązywania połączenia z aparatem bazy danych i logowanie jako włączone.
9. Po udzieleniu uprawnień ponownie **odkryj baz danych** w portalu: **->** obciążenie infrastruktury kopii zapasowej magazynu **->** na maszynie wirtualnej platformy Azure:

    ![Odnajdź baz danych w Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Alternatywnie można zautomatyzować nadawanie uprawnień, uruchamiając następujące polecenia programu PowerShell w trybie administratora. Nazwa wystąpienia jest domyślnie ustawiona na MSSQLSERVER. W razie potrzeby zmień argument Nazwa wystąpienia w skrypcie:

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

* [Dowiedz się więcej na temat](backup-sql-server-database-azure-vms.md) tworzenia kopii zapasowych baz danych SQL Server.
* [Dowiedz się więcej](restore-sql-database-azure-vm.md) o przywracaniu baz danych programu SQL Server z kopii zapasowych.
* [Dowiedz się więcej](manage-monitor-sql-database-backup.md) o zarządzaniu bazami danych programu SQL Server, dla których są tworzone kopie zapasowe.
