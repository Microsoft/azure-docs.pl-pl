---
title: Ustawienia synchronizacji serwerów DNS sieci wirtualnej w klastrze wirtualnym wystąpienia zarządzanego SQL
description: Dowiedz się, jak skonfigurować serwery DNS sieci wirtualnej w klastrze wirtualnym wystąpienia zarządzanego SQL.
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b95afe513dba2f1da9556b27ec17bcccc9fe88e1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173555"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>Ustawienia synchronizacji serwerów DNS sieci wirtualnej w klastrze wirtualnym wystąpienia zarządzanego SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule wyjaśniono, kiedy i jak synchronizować serwery DNS sieci wirtualnych w klastrze wirtualnym wystąpienia zarządzanego SQL.

## <a name="when-to-synchronize-the-dns-setting"></a>Kiedy synchronizować ustawienie DNS

Istnieje kilka scenariuszy (na przykład usługa db mail, serwery połączone z innymi wystąpieniami programu SQL Server w chmurze lub w środowisku hybrydowym), które wymagają rozpoznania prywatnych nazw hostów z poziomu usługi SQL Managed Instance. W takim przypadku należy skonfigurować niestandardowy system DNS na platformie Azure. Zobacz [Konfigurowanie niestandardowego serwera DNS dla usługi Azure SQL Managed Instance](custom-dns-configure.md), aby uzyskać szczegółowe informacje.

Jeśli ta zmiana jest zaimplementowana po utworzeniu wystąpienia zarządzanego [klastra wirtualnego](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) , należy zsynchronizować ustawienia serwerów DNS w klastrze wirtualnym z konfiguracją sieci wirtualnej.

> [!IMPORTANT]
> Ustawienie synchronizowania serwerów DNS będzie miało wpływ na wszystkie zarządzane wystąpienia hostowane w klastrze wirtualnym.

## <a name="how-to-synchronize-the-dns-setting"></a>Jak zsynchronizować ustawienie DNS

### <a name="azure-rbac-permissions-required"></a>Wymagane są uprawnienia kontroli RBAC platformy Azure

Użytkownik synchronizuje konfigurację serwera DNS musi mieć jedną z następujących ról platformy Azure:

- Rola właściciela subskrypcji lub
- Rola współautora wystąpienia zarządzanego lub
- Rola niestandardowa z następującymi uprawnieniami:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Pobierz sieć wirtualną, w której ustawienie serwerów DNS zostało zaktualizowane.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
Użyj polecenia programu PowerShell [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) , aby zsynchronizować konfigurację serwerów DNS dla wszystkich klastrów wirtualnych w podsieci.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Pobierz sieć wirtualną, w której ustawienie serwerów DNS zostało zaktualizowane.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Użycie interfejsu wiersza polecenia platformy Azure [AZ Resource Invoke-akcja](/cli/azure/resource#az_resource_invoke_action) do synchronizowania konfiguracji serwerów DNS dla wszystkich klastrów wirtualnych w podsieci.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o konfigurowaniu niestandardowej usługi DNS [Skonfiguruj niestandardowy serwer DNS dla wystąpienia zarządzanego Azure SQL](custom-dns-configure.md).
- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).
