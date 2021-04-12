---
title: Wdróż wystąpienie zarządzane SQL w puli wystąpień
titleSuffix: Azure SQL Managed Instance
description: W tym artykule opisano sposób tworzenia pul wystąpienia zarządzanego usługi Azure SQL (wersja zapoznawcza) i zarządzania nimi.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: 96c24c05f5e0dbb671a294b10ee51be411ca25ff
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285292"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Wdróż wystąpienie zarządzane Azure SQL w puli wystąpień
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera szczegółowe informacje na temat tworzenia [puli wystąpień](instance-pools-overview.md) i wdrażania wystąpienia zarządzanego usługi Azure SQL. 

## <a name="instance-pool-operations"></a>Operacje puli wystąpień

W poniższej tabeli przedstawiono dostępne operacje związane z pulami wystąpień i ich dostępność w Azure Portal i programie PowerShell.

|Polecenie|Azure Portal|PowerShell|
|:---|:---|:---|
|Tworzenie puli wystąpień|Nie|Tak|
|Zaktualizuj pulę wystąpień (ograniczoną liczbę właściwości)|Nie |Tak |
|Sprawdź użycie puli wystąpień i właściwości|Nie|Tak |
|Usuwanie puli wystąpień|Nie|Tak|
|Tworzenie wystąpienia zarządzanego wewnątrz puli wystąpień|Nie|Tak|
|Aktualizowanie użycia zasobów dla wystąpienia zarządzanego|Tak |Tak|
|Sprawdzanie użycia i właściwości wystąpienia zarządzanego|Tak|Tak|
|Usuwanie wystąpienia zarządzanego z puli|Tak|Tak|
|Tworzenie bazy danych w wystąpieniu w puli|Tak|Tak|
|Usuwanie bazy danych z wystąpienia zarządzanego SQL|Tak|Tak|

Dostępne [polecenia programu PowerShell](/powershell/module/az.sql/):

|Polecenie cmdlet |Opis |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Tworzy pulę wystąpień zarządzanych SQL. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Zwraca informacje o puli wystąpień. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Ustawia właściwości puli wystąpień w wystąpieniu zarządzanym SQL. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Usuwa pulę wystąpień w wystąpieniu zarządzanym SQL. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Zwraca informacje o użyciu puli wystąpień zarządzanych przez program SQL Server. |


Aby użyć programu PowerShell, [Zainstaluj najnowszą wersję programu PowerShell Core](/powershell/scripting/install/installing-powershell#powershell)i postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure PowerShell](/powershell/azure/install-az-ps).

W przypadku operacji związanych z wystąpieniami zarówno wewnątrz pul, jak i pojedynczymi wystąpieniami Użyj standardowych [poleceń wystąpienia zarządzanego](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances), ale właściwość *Nazwa puli wystąpień* musi być wypełniona, gdy używane są te polecenia dla wystąpienia w puli.

## <a name="deployment-process"></a>Proces wdrażania

Aby wdrożyć wystąpienie zarządzane w puli wystąpień, należy najpierw wdrożyć pulę wystąpień, czyli jednorazową operacją długotrwałą, w której czas trwania jest taki sam jak wdrożenie [pojedynczego wystąpienia utworzonego w pustej podsieci](sql-managed-instance-paas-overview.md#management-operations). Następnie można wdrożyć wystąpienie zarządzane w puli, która jest stosunkowo szybką operacją, która zazwyczaj trwa do pięciu minut. Parametr puli wystąpień musi być jawnie określony jako część tej operacji.

W publicznej wersji zapoznawczej obie akcje są obsługiwane tylko przy użyciu programu PowerShell i szablonów Azure Resource Manager. Azure Portal środowisko nie jest obecnie dostępne.

Po wdrożeniu wystąpienia zarządzanego w puli *można* użyć Azure Portal, aby zmienić jego właściwości na stronie warstwa cenowa.

## <a name="create-a-virtual-network-with-a-subnet"></a>Tworzenie sieci wirtualnej z podsiecią 

Aby umieścić wiele pul wystąpień w ramach tej samej sieci wirtualnej, zapoznaj się z następującymi artykułami:

- [Określ rozmiar podsieci sieci wirtualnej dla wystąpienia zarządzanego Azure SQL](vnet-subnet-determine-size.md).
- Utwórz nową sieć wirtualną i podsieć przy użyciu [szablonu Azure Portal](virtual-network-subnet-create-arm-template.md) lub postępuj zgodnie z instrukcjami dotyczącymi [przygotowania istniejącej sieci wirtualnej](vnet-existing-add-subnet.md).
 

## <a name="create-an-instance-pool"></a>Tworzenie puli wystąpień 

Po wykonaniu powyższych kroków można przystąpić do utworzenia puli wystąpień.

Do pul wystąpień mają zastosowanie następujące ograniczenia:

- Tylko Ogólnego przeznaczenia i 5 rdzeń są dostępne w publicznej wersji zapoznawczej.
- Nazwa puli może zawierać tylko małe litery, cyfry i łączniki i nie może rozpoczynać się od łącznika.
- Jeśli chcesz użyć Korzyść użycia hybrydowego platformy Azure, zostanie ona zastosowana na poziomie puli wystąpień. Można ustawić typ licencji podczas tworzenia puli lub zaktualizować ją w dowolnym momencie po utworzeniu.

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
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Ponieważ wdrożenie puli wystąpień jest długotrwałą operacją, przed uruchomieniem któregokolwiek z poniższych kroków w tym artykule należy poczekać na jego zakończenie.

## <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Po pomyślnym wdrożeniu puli wystąpień należy utworzyć w niej wystąpienie zarządzane.

Aby utworzyć wystąpienie zarządzane, wykonaj następujące polecenie:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

Wdrożenie wystąpienia w puli trwa kilka minut. Po utworzeniu pierwszego wystąpienia można utworzyć dodatkowe wystąpienia:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Tworzenie bazy danych 

Aby utworzyć i zarządzać bazami danych w wystąpieniu zarządzanym należącym do puli, Użyj poleceń z pojedynczym wystąpieniem.

Aby utworzyć bazę danych wewnątrz wystąpienia zarządzanego:

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
> Aby sprawdzić limity liczby baz danych na pulę wystąpień i wystąpienie zarządzane wdrożone w puli, odwiedź sekcję [limity zasobów puli wystąpień](instance-pools-overview.md#resource-limitations) .


## <a name="scale"></a>Skalowanie 


Po wypełnieniu wystąpienia zarządzanego z bazami danych mogą trafiać limity wystąpienia dotyczące magazynu lub wydajności. W takim przypadku, jeśli użycie puli nie zostało przekroczone, można skalować wystąpienie.
Skalowanie wystąpienia zarządzanego w puli jest operacją, która trwa kilka minut. Wymagania wstępne dotyczące skalowania są dostępne rdzeni wirtualnych i magazyn na poziomie puli wystąpień.

Aby zaktualizować liczbę rdzeni wirtualnych i rozmiar magazynu:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Aby zaktualizować tylko rozmiar magazynu:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Nawiązywanie połączenia 

Aby połączyć się z wystąpieniem zarządzanym w puli, wymagane są następujące dwa kroki:

1. [Włącz publiczny punkt końcowy dla tego wystąpienia](#enable-the-public-endpoint).
2. [Dodaj regułę ruchu przychodzącego do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](#add-an-inbound-rule-to-the-network-security-group).

Po zakończeniu obu kroków można nawiązać połączenie z wystąpieniem przy użyciu publicznego adresu punktu końcowego, portu i poświadczeń podanych podczas tworzenia wystąpienia. 

### <a name="enable-the-public-endpoint"></a>Włączanie publicznego punktu końcowego

W celu umożliwienia publicznego punktu końcowego dla wystąpienia można wykonać Azure Portal lub za pomocą następującego polecenia programu PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Ten parametr można również ustawić podczas tworzenia wystąpienia.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Dodawanie reguły ruchu przychodzącego do sieciowej grupy zabezpieczeń 

Ten krok można wykonać za pośrednictwem Azure Portal lub za pomocą poleceń programu PowerShell. można to zrobić w dowolnym momencie po przygotowaniu podsieci do wystąpienia zarządzanego.

Aby uzyskać szczegółowe informacje, zobacz [Zezwalanie na ruch publicznego punktu końcowego w sieciowej grupie zabezpieczeń](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-to-a-pool"></a>Przenoszenie istniejącego pojedynczego wystąpienia do puli
 
Przeniesienie wystąpień do i z puli jest jednym z publicznych ograniczeń w wersji zapoznawczej. Obejście polega na przywracaniu danych z dowolnego momentu z wystąpienia poza pulą do wystąpienia, które jest już w puli. 

Oba wystąpienia muszą znajdować się w tej samej subskrypcji i regionie. Przywracanie między regionami i subskrypcjami nie jest obecnie obsługiwane.

Ten proces ma pewien okres przestoju.

Aby przenieść istniejące bazy danych:

1. Wstrzymywanie obciążeń w wystąpieniu zarządzanym, z którego jest przeprowadzana migracja.
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

4. Wskaż aplikację nowym wystąpieniem i Wznów swoje obciążenia.

Jeśli istnieje wiele baz danych, należy powtórzyć proces dla każdej bazy danych.


## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z funkcjami i listą porównania, zobacz [funkcje wspólne SQL](../database/features-comparison.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby zapoznać się z samouczkiem dotyczącym korzystania z Azure Database Migration Service migracji, zobacz [migracja wystąpienia zarządzanego SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przez usługę SQL z wbudowaną analizą rozwiązywania problemów, zobacz temat [monitorowanie wystąpienia zarządzanego Azure SQL przy użyciu Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [Cennik wystąpienia zarządzanego SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
