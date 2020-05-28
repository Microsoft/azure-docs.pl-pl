---
title: Wdrażanie wystąpienia zarządzanego SQL w puli wystąpień
titleSuffix: Azure SQL Managed Instance
description: W tym artykule opisano sposób tworzenia pul wystąpienia zarządzanego usługi Azure SQL (wersja zapoznawcza) i zarządzania nimi.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c781e23b23f5dbaf8eba9efe4c27428ef35c7be1
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84113626"
---
# <a name="deploy-an-azure-sql-managed-instance-to-an-instance-pool"></a>Wdrażanie wystąpienia zarządzanego usługi Azure SQL w puli wystąpień
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera szczegółowe informacje na temat tworzenia [pul wystąpień](instance-pools-overview.md) i wdrażania wystąpienia zarządzanego usługi Azure SQL. 

## <a name="instance-pool-operations"></a>Operacje puli wystąpień

W poniższej tabeli przedstawiono dostępne operacje związane z pulami wystąpień i ich dostępność w Azure Portal i programie PowerShell.

|Polecenie|Azure Portal|PowerShell|
|:---|:---|:---|
|Tworzenie puli wystąpień|Nie|Yes|
|Aktualizuj pulę wystąpień (ograniczoną liczbę właściwości)|Nie |Yes |
|Sprawdź użycie i właściwości puli wystąpień|Nie|Yes |
|Usuń pulę wystąpień|Nie|Yes|
|Utwórz wystąpienie zarządzane SQL w ramach puli wystąpień|Nie|Yes|
|Aktualizuj użycie zasobów wystąpienia zarządzanego SQL|Yes |Tak|
|Sprawdź użycie i właściwości wystąpienia zarządzanego przez program SQL|Yes|Tak|
|Usuń wystąpienie zarządzane SQL z puli|Yes|Tak|
|Tworzenie bazy danych w wystąpieniu w puli|Yes|Tak|
|Usuwanie bazy danych z wystąpienia zarządzanego SQL|Yes|Tak|

Dostępne [polecenia programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Polecenie cmdlet |Opis |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Tworzy pulę wystąpień zarządzanych SQL. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Zwraca informacje o puli wystąpień. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Ustawia właściwości puli wystąpień w wystąpieniu zarządzanym SQL. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Usuwa pulę wystąpień w wystąpieniu zarządzanym SQL. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Zwraca informacje o użyciu puli wystąpień zarządzanych przez program SQL Server. |


Aby użyć programu PowerShell, [Zainstaluj najnowszą wersję programu PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)i postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

W przypadku operacji związanych z wystąpieniami zarówno wewnątrz pul, jak i pojedynczymi wystąpieniami Użyj standardowych [poleceń wystąpienia zarządzanego](api-references-create-manage-instance.md#powershell-create-and-manage-managed-instances), ale właściwość *Nazwa puli wystąpień* musi być wypełniona, gdy używane są te polecenia dla wystąpienia w puli.

## <a name="deployment-process"></a>Proces wdrażania

Aby wdrożyć wystąpienie zarządzane SQL w puli wystąpień, należy najpierw wdrożyć pulę wystąpień, czyli jednorazową operacją długotrwałą, w której czas trwania jest taki sam jak wdrożenie [pojedynczego wystąpienia utworzonego w pustej podsieci](sql-managed-instance-paas-overview.md#management-operations). Następnie można wdrożyć wystąpienia zarządzane SQL w puli, która jest stosunkowo szybką operacją, która zazwyczaj trwa do pięciu minut. Parametr puli wystąpień musi być jawnie określony jako część tej operacji.

W publicznej wersji zapoznawczej obie akcje są obsługiwane tylko przy użyciu programu PowerShell i szablonów Menedżer zasobów. Azure Portal środowisko nie jest obecnie dostępne.

Po wdrożeniu wystąpienia zarządzanego SQL w puli *można* użyć Azure Portal, aby zmienić jego właściwości na stronie warstwa cenowa.

## <a name="create-virtual-network-with-a-subnet"></a>Tworzenie sieci wirtualnej z podsiecią 

Aby umieścić wiele pul wystąpień w ramach tej samej sieci wirtualnej, zapoznaj się z następującymi artykułami:

- [Określ rozmiar podsieci sieci wirtualnej dla wystąpienia zarządzanego Azure SQL](vnet-subnet-determine-size.md).
- Utwórz nową sieć wirtualną i podsieć przy użyciu [szablonu Azure Portal](virtual-network-subnet-create-arm-template.md) lub postępuj zgodnie z instrukcjami dotyczącymi [przygotowania istniejącej sieci wirtualnej](vnet-existing-add-subnet.md).
 

## <a name="create-instance-pool"></a>Tworzenie puli wystąpień 

Po wykonaniu powyższych kroków można przystąpić do utworzenia puli wystąpień.

Do pul wystąpień mają zastosowanie następujące ograniczenia:

- Tylko Ogólnego przeznaczenia i 5 rdzeń są dostępne w publicznej wersji zapoznawczej.
- Nazwa puli może zawierać tylko małe litery, cyfry i łączniki oraz nie może rozpoczynać się od łącznika.
- Jeśli chcesz użyć AHB (Korzyść użycia hybrydowego platformy Azure), jest on stosowany na poziomie puli wystąpień. Można ustawić typ licencji podczas tworzenia puli lub zaktualizować ją w dowolnym momencie po utworzeniu.

> [!IMPORTANT]
> Wdrożenie puli wystąpień jest długotrwałą operacją, która trwa około 4,5 godzin.

Aby uzyskać parametry sieci:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Aby utworzyć pulę wystąpień:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Ponieważ wdrożenie puli wystąpień jest długotrwałą operacją, przed uruchomieniem któregokolwiek z poniższych kroków w tym artykule należy poczekać na jego zakończenie.

## <a name="create-sql-managed-instance"></a>Tworzenie wystąpienia zarządzanego SQL

Po pomyślnym wdrożeniu puli wystąpień należy utworzyć w niej wystąpienie zarządzane SQL.

Aby utworzyć wystąpienie zarządzane SQL, wykonaj następujące polecenie:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Wdrożenie wystąpienia w puli trwa kilka minut. Po utworzeniu pierwszego wystąpienia można utworzyć dodatkowe wystąpienia:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Tworzenie bazy danych 

Aby utworzyć i zarządzać bazami danych w wystąpieniu zarządzanym SQL, które znajduje się wewnątrz puli, Użyj poleceń z pojedynczym wystąpieniem.

Aby utworzyć bazę danych wewnątrz wystąpienia zarządzanego SQL:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Pobierz użycie puli 
 
Aby uzyskać listę wystąpień w puli:

```powershell
$instancePool | Get-AzSqlInstance
```


Aby uzyskać użycie zasobów puli:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Aby uzyskać szczegółowe omówienie użycia puli i wystąpień w niej:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Aby wyświetlić listę baz danych w wystąpieniu:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Istnieje limit 100 baz danych na pulę (nie na wystąpienie).


## <a name="scale"></a>Skalowanie 


Po wypełnieniu wystąpienia zarządzanego SQL z bazami danych mogą trafiać limity wystąpienia dotyczące magazynu lub wydajności. W takim przypadku, jeśli użycie puli nie zostało przekroczone, można skalować wystąpienie.
Skalowanie wystąpienia zarządzanego SQL wewnątrz puli jest operacją, która trwa kilka minut. Wymagania wstępne dotyczące skalowania są dostępne rdzeni wirtualnych i magazyn na poziomie puli wystąpień.

Aby zaktualizować liczbę rdzeni wirtualnych i rozmiar magazynu:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Aby zaktualizować tylko rozmiar magazynu:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Połącz 

Aby nawiązać połączenie z wystąpieniem zarządzanym SQL w puli, wymagane są następujące dwa kroki:

1. [Włącz publiczny punkt końcowy dla tego wystąpienia](#enable-public-endpoint).
2. [Dodaj regułę ruchu przychodzącego do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](#add-an-inbound-rule-to-the-network-security-group).

Po zakończeniu obu kroków można nawiązać połączenie z wystąpieniem przy użyciu publicznego adresu punktu końcowego, portu i poświadczeń podanych podczas tworzenia wystąpienia. 

### <a name="enable-public-endpoint"></a>Włącz publiczny punkt końcowy

W celu umożliwienia publicznego punktu końcowego dla wystąpienia można wykonać Azure Portal lub za pomocą następującego polecenia programu PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Ten parametr można również ustawić podczas tworzenia wystąpienia.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Dodawanie reguły ruchu przychodzącego do sieciowej grupy zabezpieczeń 

Ten krok można wykonać za pośrednictwem Azure Portal lub za pomocą poleceń programu PowerShell. można to zrobić w dowolnym momencie po przygotowaniu podsieci do wystąpienia zarządzanego.

Aby uzyskać szczegółowe informacje, zobacz [Zezwalanie na ruch publicznego punktu końcowego w sieciowej grupie zabezpieczeń](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-existing-single-instance-to-pool"></a>Przenieś istniejące pojedyncze wystąpienie do puli
 
Przeniesienie wystąpień do i z puli jest jednym z publicznych ograniczeń w wersji zapoznawczej. Obejście polega na przywracaniu danych z dowolnego momentu z wystąpienia poza pulą do wystąpienia, które jest już w puli. 

Oba wystąpienia muszą znajdować się w tej samej subskrypcji i regionie. Przywracanie między regionami i subskrypcjami nie jest obecnie obsługiwane.

Ten proces ma pewien okres przestoju.

Aby przenieść istniejące bazy danych:

1. Wstrzymywanie obciążeń w wystąpieniu zarządzanym SQL, z którego jest przeprowadzana migracja.
2. Generuj skrypty umożliwiające tworzenie systemowych baz danych i wykonywanie ich w wystąpieniu znajdującym się wewnątrz puli wystąpień.
3. Wykonaj przywracanie do punktu w czasie dla każdej bazy danych z jednego wystąpienia do wystąpienia w puli.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Wskaż aplikację nowym wystąpieniem i Wznów obciążenie.

Jeśli istnieje wiele baz danych, należy powtórzyć proces dla każdej bazy danych.


## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z funkcjami i listą porównania, zobacz [funkcje wspólne SQL](../database/features-comparison.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- Przewodnik Szybki Start, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, znajduje się w temacie [Tworzenie wystąpienia zarządzanego SQL](instance-create-quickstart.md).
- Aby zapoznać się z samouczkiem korzystającym z Azure Database Migration Service (DMS) do migracji, zobacz [migracja wystąpienia zarządzanego SQL za pomocą usługi DMS](../../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przez usługę SQL z wbudowaną analizą rozwiązywania problemów, zobacz temat [monitorowanie wystąpienia zarządzanego Azure SQL przy użyciu Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [Cennik wystąpienia zarządzanego SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
