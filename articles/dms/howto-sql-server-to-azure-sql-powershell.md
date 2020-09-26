---
title: 'PowerShell: Migruj SQL Server do SQL Database'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację bazy danych z SQL Server do Azure SQL Database przy użyciu Azure PowerShell z Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a2d0ff6810326f7f375595e8dcebbe81b55055ca
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330353"
---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-azure-powershell"></a>Migrowanie bazy danych SQL Server do Azure SQL Database przy użyciu Azure PowerShell

W tym artykule przeprowadzisz migrację bazy danych **Adventureworks2012** do lokalnego wystąpienia SQL Server 2016 lub nowszego do Azure SQL Database przy użyciu Microsoft Azure PowerShell. Bazy danych można migrować z wystąpienia SQL Server do Azure SQL Database przy użyciu `Az.DataMigration` modułu w Microsoft Azure PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Utwórz grupę zasobów.
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Utwórz projekt migracji w wystąpieniu Azure Database Migration Service.
> * Uruchamianie migracji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te kroki, potrzebne są:

* [SQL Server 2016 lub nowszy](https://www.microsoft.com/sql-server/sql-server-downloads) (dowolna wersja)
* Aby włączyć protokół TCP/IP, który jest domyślnie wyłączony z instalacją SQL Server Express. Włącz protokół TCP/IP, wykonując czynności opisane w artykule [Włączanie lub wyłączanie protokołu sieciowego serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Aby skonfigurować [zaporę systemu Windows na potrzeby dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Wystąpienie usługi Azure SQL Database. Można utworzyć wystąpienie Azure SQL Database, postępując zgodnie ze szczegółowymi informacjami w artykule [Tworzenie bazy danych w Azure SQL Database w Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 lub nowszy.
* W celu utworzenia Microsoft Azure Virtual Network przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia Azure Database Migration Service z połączeniem lokacja-lokacja z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Ukończenie oceny lokalnej bazy danych i migracji schematu przy użyciu Data Migration Assistant zgodnie z opisem w artykule [wykonywanie oceny migracji SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
* Pobieranie i Instalowanie modułu AZ. datamigration z Galeria programu PowerShell przy użyciu [polecenia cmdlet Install-module programu PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); Upewnij się, że okno polecenia programu PowerShell zostało otwarte przy użyciu opcji Uruchom jako administrator.
* Aby upewnić się, że poświadczenia używane do nawiązania połączenia z wystąpieniem źródła SQL Server ma uprawnienie [serwer kontroli](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Aby upewnić się, że poświadczenia używane do nawiązania połączenia z docelowym wystąpieniem usługi Azure SQL DB mają uprawnienie Kontrola bazy danych dla docelowych baz danych Azure SQL Database.
* Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logowanie do subskrypcji Microsoft Azure

Aby zalogować się do subskrypcji platformy Azure przy użyciu programu PowerShell, Skorzystaj z instrukcji zamieszczonych w artykule [Logowanie za pomocą Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) .

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, aby móc utworzyć maszynę wirtualną.

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) .

Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w regionie *wschodnim* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

Nowe wystąpienie Azure Database Migration Service można utworzyć przy użyciu `New-AzDataMigrationService` polecenia cmdlet. To polecenie cmdlet oczekuje następujących wymaganych parametrów:

* *Nazwa grupy zasobów platformy Azure*. Za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) można utworzyć grupę zasobów platformy Azure, jak pokazano wcześniej, i podać jej nazwę jako parametr.
* *Nazwa usługi*. Ciąg, który odnosi się do żądanej unikatowej nazwy usługi dla Azure Database Migration Service 
* *Lokalizacja*. Określa lokalizację usługi. Określ lokalizację centrum danych platformy Azure, np. Zachodnie stany USA lub Azja Południowo-Wschodnia
* *Jednostka SKU*. Ten parametr odnosi się do nazwy jednostki SKU DMS. Obecnie obsługiwana Nazwa jednostki SKU to *GeneralPurpose_4vCores*.
* *Identyfikator podsieci wirtualnej*. Aby utworzyć podsieć, można użyć polecenia cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) . 

Poniższy przykład tworzy usługę o nazwie *MyDMS* w grupie zasobów *MyDMSResourceGroup* znajdującej się w regionie *Wschodnie stany USA* przy użyciu sieci wirtualnej o nazwie *MyVNET* i podsieci o nazwie Moja *podsieć*.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia Azure Database Migration Service Utwórz projekt migracji. Projekt Azure Database Migration Service wymaga informacji o połączeniu zarówno dla wystąpienia źródłowego, jak i docelowego, jak również listy baz danych, które mają być migrowane w ramach projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Utwórz obiekt informacji o połączeniu z bazą danych dla połączeń źródłowych i docelowych

Obiekt informacji o połączeniu z bazą danych można utworzyć przy użyciu `New-AzDmsConnInfo` polecenia cmdlet. To polecenie cmdlet oczekuje następujących parametrów:

* *Serwertype*. Typ żądanego połączenia z bazą danych, na przykład SQL, Oracle lub MySQL. Użyj programu SQL dla SQL Server i usługi Azure SQL.
* *Źródło danych*. Nazwa lub adres IP wystąpienia SQL Server lub Azure SQL Database.
* *AuthType*. Typ uwierzytelniania dla połączenia, który może mieć wartość sqlauthentication lub WindowsAuthentication.
* Parametr *TrustServerCertificate* ustawia wartość wskazującą, czy kanał jest szyfrowany podczas pomijania przechodzenia łańcucha certyfikatów w celu zweryfikowania zaufania. Wartość może być równa true lub false.

Poniższy przykład tworzy obiekt info Connection dla SQL Server źródłowej o nazwie MySourceSQLServer przy użyciu uwierzytelniania SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

W następnym przykładzie pokazano tworzenie informacji o połączeniu dla serwera o nazwie SQLAzureTarget przy użyciu uwierzytelniania SQL:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Podaj bazy danych dla projektu migracji

Utwórz listę `AzDataMigrationDatabaseInfo` obiektów, które określają bazy danych w ramach projektu migracji bazy danych platformy Azure, który można podać jako parametr do tworzenia projektu. Polecenia cmdlet `New-AzDataMigrationDatabaseInfo` można użyć do utworzenia AzDataMigrationDatabaseInfo. 

Poniższy przykład tworzy `AzDataMigrationDatabaseInfo` projekt dla bazy danych **AdventureWorks2016** i dodaje ją do listy, która ma zostać dostarczona jako parametr do tworzenia projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Utwórz obiekt projektu

Na koniec można utworzyć projekt migracji bazy danych platformy Azure o nazwie *MyDMSProject* , który znajduje się w *regionie Wschodnie stany USA* przy użyciu `New-AzDataMigrationProject` i dodając wcześniej utworzone połączenie źródłowe i docelowe oraz listę baz danych do migracji.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Na koniec Utwórz i uruchom zadanie migracji bazy danych platformy Azure. Zadanie migracji bazy danych platformy Azure wymaga informacji o poświadczeniach połączenia zarówno dla źródła, jak i docelowej listy tabel bazy danych, które mają zostać zmigrowane, a także do informacji już podanych w projekcie utworzonym jako warunek wstępny.

### <a name="create-credential-parameters-for-source-and-target"></a>Utwórz parametry poświadczeń dla źródła i celu

Poświadczenia zabezpieczeń połączeń można utworzyć jako obiekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) .

Poniższy przykład pokazuje Tworzenie obiektów *PSCredential* dla połączeń źródłowych i docelowych, dostarczając hasła jako zmienne ciągów *$sourcePassword* i *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Utwórz mapę tabeli i wybierz parametry źródłowe i docelowe dla migracji

Innym parametrem wymaganym do migracji jest mapowanie tabel z lokalizacji źródłowej do docelowej, która ma zostać zmigrowana. Utwórz słownik tabel, który zawiera mapowanie między tabelami źródłowymi i docelowymi do migracji. Poniższy przykład ilustruje mapowanie między tabelą źródłową i docelową schemat zasobów ludzkich dla bazy danych AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Następnym krokiem jest wybranie źródłowych i docelowych baz danych oraz udostępnienie mapowania tabeli w celu przeprowadzenia migracji jako parametru za pomocą `New-AzDmsSelectedDB` polecenia cmdlet, jak pokazano w następującym przykładzie:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Utwórz zadanie migracji i uruchom je

Użyj `New-AzDataMigrationTask` polecenia cmdlet, aby utworzyć i uruchomić zadanie migracji. To polecenie cmdlet oczekuje następujących parametrów:

* *TaskType*. Typ zadania migracji, które ma zostać utworzone dla SQL Server do Azure SQL Database migracji typu *MigrateSqlServerSqlDb* jest oczekiwany. 
* *Nazwa grupy zasobów*. Nazwa grupy zasobów platformy Azure, w której ma zostać utworzone zadanie.
* *Nazwa ServiceName*. Wystąpienie Azure Database Migration Service, w którym ma zostać utworzone zadanie.
* *ProjectName*. Nazwa projektu Azure Database Migration Service, w którym ma zostać utworzone zadanie. 
* *Zadaniename*. Nazwa zadania do utworzenia. 
* *SourceConnection*. Obiekt AzDmsConnInfo reprezentujący połączenie SQL Server źródłowej.
* *TargetConnection*. Obiekt AzDmsConnInfo reprezentujący docelowe połączenie Azure SQL Database.
* *SourceCred*. Obiekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) do nawiązywania połączenia z serwerem źródłowym.
* *TargetCred*. Obiekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) do nawiązywania połączenia z serwerem docelowym.
* *SelectedDatabase*. Obiekt AzDataMigrationSelectedDB reprezentujący mapowanie źródłowe i docelowe bazy danych.
* *SchemaValidation*. (opcjonalny, parametr przełącznika) Po migracji program wykonuje porównanie informacji o schemacie między źródłowym i docelowym.
* *DataIntegrityValidation*. (opcjonalny, parametr przełącznika) Po migracji program wykonuje weryfikację integralności danych opartą na sumie kontrolnej między źródłem a celem.
* *QueryAnalysisValidation*. (opcjonalny, parametr przełącznika) Po migracji program wykonuje szybką i inteligentną analizę zapytań, pobierając zapytania ze źródłowej bazy danych i wykonuje je w miejscu docelowym.

Poniższy przykład tworzy i uruchamia zadanie migracji o nazwie myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

Poniższy przykład tworzy i uruchamia takie samo zadanie migracji jak powyżej, ale wykonuje także wszystkie trzy walidacje:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>Monitorowanie migracji

Możesz monitorować uruchomione zadanie migracji, wykonując zapytania o Właściwość State zadania, jak pokazano w następującym przykładzie:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Usuwanie wystąpienia DMS

Po zakończeniu migracji można usunąć wystąpienie usługi Azure DMS:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Następny krok

* Zapoznaj się ze wskazówkami dotyczącymi migracji w [przewodniku migracji bazy danych](https://datamigration.microsoft.com/)firmy Microsoft.
