---
title: Konfigurowanie grupy dostępności (program PowerShell & interfejsu wiersza polecenia Az)
description: Użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć klaster trybu failover systemu Windows, odbiornik grupy dostępności i wewnętrzny równoważenie obciążenia na maszynie SQL Server wirtualnej na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: ffd4ec6eff94589abbc8af70ecf9c0f7dc168962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766937"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Konfigurowanie grupy dostępności dla grupy dostępności na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell SQL Server Az 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano sposób wdrażania klastra trybu failover systemu Windows przy użyciu programu [PowerShell](/powershell/scripting/install/installing-powershell) lub interfejsu wiersza polecenia platformy [Azure,](/cli/azure/sql/vm) dodawania maszyn wirtualnych usługi SQL Server do klastra oraz tworzenia wewnętrznego równoważenia obciążenia i odbiornika dla zawsze wł. grupy dostępności. 

Wdrażanie grupy dostępności jest nadal wykonywane ręcznie za pośrednictwem programu SQL Server Management Studio (SSMS) lub języka Transact-SQL (T-SQL). 

Chociaż w tym artykule do skonfigurowania środowiska grupy dostępności używany jest program PowerShell i interfejs wiersza polecenia Az, [](availability-group-manually-configure-tutorial.md) można to zrobić również z witryny [Azure Portal](availability-group-azure-portal-configure.md), przy użyciu szablonów szybkiego startu platformy [Azure](availability-group-quickstart-template-configure.md)lub ręcznie. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować zawsze wł. grupę dostępności, musisz mieć następujące wymagania wstępne: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Grupa zasobów z kontrolerem domeny. 
- Co najmniej jedna maszyny wirtualne przyłączone do domeny na platformie Azure z systemem SQL Server  [2016 (lub nowszym)](./create-sql-vm-portal.md) w tym samym zestawie dostępności lub różnych strefach dostępności, które zostały zarejestrowane przy użyciu rozszerzenia agenta SQL [IaaS.](sql-agent-extension-manually-register-single-vm.md)   
- Najnowsza wersja programu [PowerShell lub](/powershell/scripting/install/installing-powershell) interfejsu wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli) 
- Dwa dostępne (nie używane przez żadne jednostki) adresy IP. Jeden z nich jest dla wewnętrznego równoważenia obciążenia. Drugi dotyczy odbiornika grupy dostępności w tej samej podsieci co grupa dostępności. Jeśli używasz istniejącego równoważenia obciążenia, potrzebujesz tylko jednego dostępnego adresu IP dla odbiornika grupy dostępności. 

## <a name="permissions"></a>Uprawnienia

Do skonfigurowania zawsze dostępnej grupy dostępności przy użyciu interfejsu wiersza polecenia platformy Azure potrzebne są następujące uprawnienia konta: 

- Istniejące konto użytkownika domeny z **uprawnieniem Utwórz obiekt komputera** w domenie. Na przykład konto administratora domeny zwykle ma wystarczające uprawnienia (na przykład: account@domain.com ). _To konto powinno być również częścią lokalnej grupy administratorów na każdej maszynie wirtualnej w celu utworzenia klastra._
- Konto użytkownika domeny, które kontroluje SQL Server. 
 
## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Klaster musi mieć konto magazynu, aby pełnić usługę w chmurze. Możesz użyć dowolnego istniejącego konta magazynu lub utworzyć nowe konto magazynu. Jeśli chcesz użyć istniejącego konta magazynu, przejdź do następnej sekcji. 

Poniższy fragment kodu tworzy konto magazynu: 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Jeśli używasz nieaktualnej wersji interfejsu wiersza polecenia platformy Azure, może zostać `az sql: 'vm' is not in the 'az sql' command group` wyświetlony błąd. Pobierz [najnowszą wersję interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli-windows) aby ominić ten błąd.


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Definiowanie metadanych klastra

Grupa poleceń [az sql vm group](/cli/azure/sql/vm/group) interfejsu wiersza polecenia platformy Azure zarządza metadanymi usługi klastra trybu failover systemu Windows Server (WSFC), która hostuje grupę dostępności. Metadane klastra obejmują domenę usługi Active Directory, konta klastra, konta magazynu, które mają być używane jako SQL Server w chmurze. Użyj [polecenia az sql vm group create,](/cli/azure/sql/vm/group#az_sql_vm_group_create) aby zdefiniować metadane dla usługi WSFC, tak aby po dodaniu pierwszej maszyny SQL Server wirtualnej klaster został utworzony zgodnie z definicją. 

Poniższy fragment kodu definiuje metadane klastra:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Dodawanie maszyn wirtualnych do klastra

Dodanie pierwszej maszyny SQL Server wirtualnej do klastra powoduje utworzenie klastra. Polecenie [az sql vm add-to-group](/cli/azure/sql/vm#az_sql-vm_add_to_group) tworzy klaster o podanej wcześniej nazwie, instaluje rolę klastra na maszynach wirtualnych SQL Server i dodaje je do klastra. Kolejne zastosowania polecenia dodają więcej `az sql vm add-to-group` SQL Server wirtualnych do nowo utworzonego klastra. 

Poniższy fragment kodu tworzy klaster i dodaje do niego SQL Server maszynę wirtualną: 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Użyj tego polecenia, aby dodać SQL Server maszyn wirtualnych do klastra. `-n`Zmodyfikuj tylko parametr SQL Server maszyny wirtualnej. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Weryfikowanie klastra 

Aby klaster trybu failover był obsługiwany przez firmę Microsoft, musi przejść walidację klastra. Połącz się z maszyną wirtualną przy użyciu preferowanej metody, takiej jak Remote Desktop Protocol (RDP) i sprawdź, czy klaster pomyślnie przejdzie walidację, zanim przejdziesz dalej. Jeśli tego nie zrobisz, klaster będzie w stanie nieobsługiwanym. 

Klaster można zweryfikować przy użyciu Menedżer klastra trybu failover (FCM) lub następującego polecenia programu PowerShell:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Tworzenie grupy dostępności

Ręcznie utwórz grupę dostępności w zwykły sposób przy użyciu programu [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [programu PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)lub [języka Transact-SQL.](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 

>[!IMPORTANT]
> W *tej* chwili nie należy tworzyć odbiornika, ponieważ odbywa się to za pośrednictwem interfejsu wiersza polecenia platformy Azure w poniższych sekcjach.  

## <a name="create-internal-load-balancer"></a>Tworzenie wewnętrznego równoważenia obciążenia

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Odbiornik zawsze wł. grupy dostępności wymaga wewnętrznego wystąpienia Azure Load Balancer. Wewnętrzny program równoważenia obciążenia udostępnia "zmienny" adres IP dla odbiornika grupy dostępności, co umożliwia szybsze włączenie trybu failover i ponowne nawiązanie połączenia. Jeśli maszyny SQL Server wirtualne w grupie dostępności są częścią tego samego zestawu dostępności, można użyć podstawowego równoważenia obciążenia. W przeciwnym razie należy użyć standardowego równoważenia obciążenia.  

> [!NOTE]
> Wewnętrzny równoważenie obciążenia powinien być w tej samej sieci wirtualnej co SQL Server maszyn wirtualnych. 

Poniższy fragment kodu tworzy wewnętrzny równoważenie obciążenia:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> Zasób publicznego adresu IP dla każdej maszyny SQL Server wirtualnej powinien mieć standardową SKU, aby był zgodny z usługą Load Balancer w standardowych usługach. Aby określić sku zasobu publicznego adresu IP maszyny wirtualnej, przejdź do grupy **zasobów,** wybierz zasób publicznego adresu **IP** dla  żądanej maszyny wirtualnej usługi SQL Server i znajdź wartość w obszarze **SKU** w okienku Przegląd.  

## <a name="create-listener"></a>Tworzenie odbiornika

Po ręcznym utworzeniu grupy dostępności możesz utworzyć odbiornik przy użyciu polecenia [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_create). 

Identyfikator *zasobu podsieci jest* wartością dołączaną do identyfikatora zasobu sieci `/subnets/<subnetname>` wirtualnej. Aby zidentyfikować identyfikator zasobu podsieci:
   1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com). 
   1. Wybierz zasób sieci wirtualnej. 
   1. Wybierz **pozycję** Właściwości w **okienku** Ustawienia. 
   1. Zidentyfikuj identyfikator zasobu sieci wirtualnej i dołącz na końcu, `/subnets/<subnetname>` aby utworzyć identyfikator zasobu podsieci. Na przykład:
      - Identyfikator zasobu sieci wirtualnej to: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Nazwa podsieci to: `default`
      - W związku z tym identyfikator zasobu podsieci to: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Poniższy fragment kodu tworzy odbiornik grupy dostępności:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Modyfikowanie liczby replik 
Istnieje dodatkowa warstwa złożoności podczas wdrażania grupy dostępności na SQL Server wirtualnych hostowanych na platformie Azure. Dostawca zasobów i grupa maszyn wirtualnych zarządzają teraz zasobami. W związku z tym podczas dodawania lub usuwania replik w grupie dostępności istnieje dodatkowy krok aktualizowania metadanych odbiornika przy użyciu informacji o SQL Server wirtualnych. Podczas modyfikowania liczby replik w grupie dostępności należy również użyć polecenia [az sql vm group ag-listener update,](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_update) aby zaktualizować odbiornik przy użyciu metadanych maszyn wirtualnych SQL Server wirtualnych. 


### <a name="add-a-replica"></a>Dodawanie repliki

Aby dodać nową replikę do grupy dostępności:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Dodaj SQL Server wirtualną do grupy klastrów:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Użyj SQL Server Management Studio, aby dodać SQL Server jako replikę w grupie dostępności.
1. Dodaj metadane SQL Server wirtualnej do odbiornika:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Dodaj SQL Server wirtualną do grupy klastrów:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. Użyj SQL Server Management Studio, aby dodać SQL Server jako replikę w grupie dostępności.
1. Dodaj metadane SQL Server wirtualnej do odbiornika:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Usuwanie repliki

Aby usunąć replikę z grupy dostępności:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Usuń replikę z grupy dostępności przy użyciu SQL Server Management Studio. 
1. Usuń metadane SQL Server wirtualnej z odbiornika:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Usuń maszynę SQL Server wirtualną z klastra:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Usuń replikę z grupy dostępności przy użyciu SQL Server Management Studio. 
1. Usuń metadane SQL Server wirtualnej z odbiornika:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Usuń maszynę SQL Server wirtualną z klastra:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Usuwanie odbiornika
Jeśli później konieczne będzie usunięcie odbiornika grupy dostępności skonfigurowanego przy użyciu interfejsu wiersza polecenia platformy Azure, musisz przejść przez rozszerzenie agenta SQL IaaS. Ponieważ odbiornik jest rejestrowany za pośrednictwem rozszerzenia agenta SQL IaaS, samo usunięcie go za pomocą SQL Server Management Studio jest niewystarczające. 

Najlepszą metodą jest usunięcie go za pośrednictwem rozszerzenia agenta SQL IaaS przy użyciu poniższego fragmentu kodu w interfejsie wiersza polecenia platformy Azure. Spowoduje to usunięcie metadanych odbiornika grupy dostępności z rozszerzenia agenta SQL IaaS. Ponadto fizycznie usuwa odbiornik z grupy dostępności. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Usuwanie klastra

Usuń wszystkie węzły z klastra, aby go zniszczyć, a następnie usuń metadane klastra z rozszerzenia agenta SQL IaaS. Możesz to zrobić przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw usuń wszystkie maszyny wirtualne SQL Server z klastra: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Jeśli są to jedyne maszyny wirtualne w klastrze, klaster zostanie zniszczona. Jeśli w klastrze istnieją inne maszyny wirtualne poza usuniętymi maszynami SQL Server, pozostałe maszyny wirtualne nie zostaną usunięte i klaster nie zostanie zniszczona. 

Następnie usuń metadane klastra z rozszerzenia agenta SQL IaaS: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Najpierw usuń wszystkie maszyny SQL Server wirtualne z klastra. Spowoduje to fizyczne usunięcie węzłów z klastra i zniszczenie klastra: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Jeśli są to jedyne maszyny wirtualne w klastrze, klaster zostanie zniszczona. Jeśli w klastrze istnieją inne maszyny wirtualne poza usuniętymi maszynami SQL Server, pozostałe maszyny wirtualne nie zostaną usunięte i klaster nie zostanie zniszczona. 

Następnie usuń metadane klastra z rozszerzenia agenta SQL IaaS: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Omówienie maszyn SQL Server wirtualnych](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Często zadawane pytania dotyczące maszyn SQL Server wirtualnych](frequently-asked-questions-faq.md)
* [Informacje o wersji dla maszyn SQL Server wirtualnych](../../database/doc-changes-updates-release-notes.md)
* [Przełączanie modeli licencjonowania dla maszyny SQL Server wirtualnej](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Omówienie zawsze dostępnych grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfiguracja wystąpienia serwera dla zawsze wł. grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administrowanie serwerem grupy dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorowanie grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Omówienie instrukcji języka Transact-SQL dla zawsze dostępnych grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Omówienie poleceń cmdlet programu PowerShell dla zawsze wł. grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)