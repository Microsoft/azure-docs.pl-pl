---
title: Przewodniki dotyczące pul wystąpień (wersja zapoznawcza)
description: W tym artykule opisano sposób tworzenia pul wystąpień Azure SQL Database (wersja zapoznawcza) i zarządzania nimi.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79299366"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Pule wystąpień Azure SQL Database (wersja zapoznawcza) — Przewodnik

Ten artykuł zawiera szczegółowe informacje na temat tworzenia [pul wystąpień](sql-database-instance-pools.md)i zarządzania nimi.

## <a name="instance-pool-operations"></a>Operacje puli wystąpień

W poniższej tabeli przedstawiono dostępne operacje związane z pulami wystąpień i ich dostępność w Azure Portal i programie PowerShell.

|Polecenie|Azure Portal|PowerShell|
|:---|:---|:---|
|Tworzenie puli wystąpień|Nie|Tak|
|Aktualizuj pulę wystąpień (ograniczoną liczbę właściwości)|Nie |Tak |
|Sprawdź użycie i właściwości puli wystąpień|Nie|Tak |
|Usuń pulę wystąpień|Nie|Tak|
|Utwórz wystąpienie zarządzane w puli wystąpień|Nie|Tak|
|Aktualizuj użycie zasobów wystąpienia zarządzanego|Tak |Tak|
|Sprawdź użycie wystąpienia zarządzanego i właściwości|Tak|Tak|
|Usuń wystąpienie zarządzane z puli|Tak|Tak|
|Tworzenie bazy danych w wystąpieniu zarządzanym umieszczonym w puli|Tak|Tak|
|Usuwanie bazy danych z wystąpienia zarządzanego|Tak|Tak|

Dostępne [polecenia programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Polecenie cmdlet |Opis |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Tworzy pulę wystąpień Azure SQL Database. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Zwraca informacje o puli wystąpień usługi Azure SQL. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Ustawia właściwości dla puli wystąpień Azure SQL Database. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Usuwa pulę wystąpień Azure SQL Database. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Zwraca informacje o użyciu puli wystąpień usługi Azure SQL. |


Aby użyć programu PowerShell, [Zainstaluj najnowszą wersję programu PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)i postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

W przypadku operacji związanych z wystąpieniami zarówno wewnątrz pul, jak i pojedynczymi wystąpieniami Użyj standardowych [poleceń wystąpienia zarządzanego](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances), ale właściwość *Nazwa puli wystąpień* musi być wypełniona, gdy używane są te polecenia dla wystąpienia w puli.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Jak wdrażać wystąpienia zarządzane w pulach

Proces wdrażania wystąpienia w puli obejmuje następujące dwa kroki:

1. Wdrożenie jednej puli wystąpień. Jest to długotrwała operacja, w której czas trwania jest taki sam jak wdrażanie [pojedynczego wystąpienia utworzonego w pustej podsieci](sql-database-managed-instance.md#managed-instance-management-operations).

2. Wdrożenie powtarzające wystąpienie w puli wystąpień. Parametr puli wystąpień musi być jawnie określony jako część tej operacji. Jest to stosunkowo szybka operacja, która zazwyczaj trwa do 5 minut.

W publicznej wersji zapoznawczej obie czynności są obsługiwane tylko przy użyciu programu PowerShell i szablonów Menedżer zasobów. Azure Portal środowisko nie jest obecnie dostępne.

Po wdrożeniu wystąpienia zarządzanego w puli *można* użyć Azure Portal, aby zmienić jego właściwości na stronie warstwa cenowa.


## <a name="create-an-instance-pool"></a>Tworzenie puli wystąpień

Aby utworzyć pulę wystąpień:

1. [Utwórz sieć wirtualną z podsiecią](#create-a-virtual-network-with-a-subnet).
2. [Utwórz pulę wystąpień](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Tworzenie sieci wirtualnej z podsiecią 

Aby umieścić wiele pul wystąpień w ramach tej samej sieci wirtualnej, zapoznaj się z następującymi artykułami:

- [Określ rozmiar podsieci sieci wirtualnej dla Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Utwórz nową sieć wirtualną i podsieć przy użyciu [szablonu Azure Portal](sql-database-managed-instance-create-vnet-subnet.md) lub postępuj zgodnie z instrukcjami dotyczącymi [przygotowania istniejącej sieci wirtualnej](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Tworzenie puli wystąpień 

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

## <a name="create-a-managed-instance-inside-the-pool"></a>Tworzenie wystąpienia zarządzanego wewnątrz puli 

Po pomyślnym wdrożeniu puli wystąpień należy utworzyć w niej wystąpienie.

Aby utworzyć wystąpienie zarządzane, wykonaj następujące polecenie:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Wdrożenie wystąpienia w puli trwa kilka minut. Po utworzeniu pierwszego wystąpienia można utworzyć dodatkowe wystąpienia:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Tworzenie bazy danych wewnątrz wystąpienia 

Aby utworzyć i zarządzać bazami danych w wystąpieniu zarządzanym należącym do puli, Użyj poleceń z pojedynczym wystąpieniem.

Aby utworzyć bazę danych wewnątrz wystąpienia zarządzanego:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Pobierz użycie puli wystąpień 
 
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


## <a name="scale-a-managed-instance-inside-a-pool"></a>Skalowanie wystąpienia zarządzanego wewnątrz puli 


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



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Nawiązywanie połączenia z wystąpieniem zarządzanym w puli

Aby połączyć się z wystąpieniem zarządzanym w puli, wymagane są następujące dwa kroki:

1. [Włącz publiczny punkt końcowy dla tego wystąpienia](#enable-the-public-endpoint-for-the-instance).
2. [Dodaj regułę ruchu przychodzącego do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](#add-an-inbound-rule-to-the-network-security-group).

Po zakończeniu obu kroków można nawiązać połączenie z wystąpieniem przy użyciu publicznego adresu punktu końcowego, portu i poświadczeń podanych podczas tworzenia wystąpienia. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Włącz publiczny punkt końcowy dla wystąpienia

W celu umożliwienia publicznego punktu końcowego dla wystąpienia można wykonać Azure Portal lub za pomocą następującego polecenia programu PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Ten parametr można również ustawić podczas tworzenia wystąpienia.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Dodawanie reguły ruchu przychodzącego do sieciowej grupy zabezpieczeń 

Ten krok można wykonać za pośrednictwem Azure Portal lub za pomocą poleceń programu PowerShell. można to zrobić w dowolnym momencie po przygotowaniu podsieci do wystąpienia zarządzanego.

Aby uzyskać szczegółowe informacje, zobacz [Zezwalanie na ruch publicznego punktu końcowego w sieciowej grupie zabezpieczeń](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Przenoszenie istniejącego pojedynczego wystąpienia wewnątrz puli wystąpień 
 
Przeniesienie wystąpień do i z puli jest jednym z publicznych ograniczeń w wersji zapoznawczej. Obejście, którego można użyć, polega na przywróceniu bazy danych do punktu w czasie z wystąpienia poza pulą do wystąpienia, które jest już w puli. 

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

4. Wskaż aplikację nowym wystąpieniem i Wznów obciążenie.

Jeśli istnieje wiele baz danych, należy powtórzyć proces dla każdej bazy danych.


## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z funkcjami i listą porównania, zobacz [funkcje wspólne SQL](sql-database-features.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby zapoznać się z samouczkiem korzystającym z Azure Database Migration Service (DMS) do migracji, zobacz [migracja wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przy użyciu wbudowanej analizy rozwiązywania problemów, zobacz [monitorowanie Azure SQL Database przy użyciu Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [SQL Database cenach wystąpienia zarządzanego](https://azure.microsoft.com/pricing/details/sql-database/managed/).
