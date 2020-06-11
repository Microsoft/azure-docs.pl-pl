---
title: Konfigurowanie grupy dostępności przy użyciu interfejsu wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure do utworzenia klastra trybu failover systemu Windows, odbiornika grupy dostępności i wewnętrznego modułu równoważenia obciążenia na SQL Server maszynie wirtualnej na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3366438012ecc3395e7f4ae3774316ef1ddcd3b9
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669345"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-azure-vm"></a>Użyj interfejsu wiersza polecenia platformy Azure, aby skonfigurować zawsze włączona Grupa dostępności dla SQL Server na maszynie wirtualnej platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano, jak za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/vm?view=azure-cli-latest/) wdrożyć klaster trybu failover systemu Windows, dodać SQL Server maszyny wirtualne do klastra i utworzyć wewnętrzny moduł równoważenia obciążenia i odbiornik dla zawsze włączonej grupy dostępności. Wdrożenie zawsze włączonych grup dostępności jest nadal wykonywane ręcznie za pomocą narzędzia SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Wymagania wstępne
Aby zautomatyzować instalację zawsze włączonych grup dostępności przy użyciu interfejsu wiersza polecenia platformy Azure, musisz mieć następujące wymagania wstępne: 
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Grupa zasobów z kontrolerem domeny. 
- Co najmniej jedna maszyna wirtualna przyłączona [do domeny na platformie Azure z systemem SQL Server 2016 (lub nowszym)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) w *tym samym zestawie dostępności lub w różnych strefach dostępności* [zarejestrowanych u dostawcy zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md).  
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 
- Dostępne są dwa adresy IP (nieużywane przez żadne jednostki). Jeden z nich dotyczy wewnętrznego modułu równoważenia obciążenia. Druga dotyczy odbiornika grupy dostępności w tej samej podsieci, w której znajduje się grupa dostępności. Jeśli używasz istniejącego modułu równoważenia obciążenia, dla odbiornika grupy dostępności jest wymagany tylko jeden dostępny adres IP. 

## <a name="permissions"></a>Uprawnienia
Aby skonfigurować zawsze włączone grupy dostępności przy użyciu interfejsu wiersza polecenia platformy Azure, potrzebne są następujące uprawnienia konta: 

- Istniejące konto użytkownika domeny, które ma uprawnienie **Tworzenie obiektu komputera** w domenie. Na przykład konto administratora domeny ma zwykle wystarczające uprawnienia (na przykład: account@domain.com ). _To konto powinno być również częścią lokalnej grupy administratorów na każdej maszynie wirtualnej w celu utworzenia klastra._
- Konto użytkownika domeny kontrolujące SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Krok 1. Tworzenie konta magazynu jako monitora chmury
Klaster wymaga konta magazynu do działania jako monitor chmury. Możesz użyć dowolnego istniejącego konta magazynu lub utworzyć nowe konto magazynu. Jeśli chcesz użyć istniejącego konta magazynu, przejdź do następnej sekcji. 

Poniższy fragment kodu tworzy konto magazynu: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> `az sql: 'vm' is not in the 'az sql' command group`Jeśli używasz nieaktualnej wersji interfejsu wiersza polecenia platformy Azure, może wystąpić błąd. Pobierz [najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) , aby uzyskać poprzedni błąd.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Krok 2. Definiowanie metadanych klastra trybu failover systemu Windows
W interfejsie wiersza polecenia platformy Azure [AZ SQL VM Group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) Group (usługa) zarządza metadanymi usługi Windows Server failover Cluster (WSFC), która hostuje grupę dostępności. Metadane klastra obejmują domenę Active Directory, konta klastra, konta magazynu, które mają być używane jako monitor chmury i wersja SQL Server. Użyj [AZ SQL VM Group Create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) w celu zdefiniowania metadanych usługi WSFC, aby podczas dodawania pierwszej SQL SERVEREJ maszyny wirtualnej klaster został utworzony zgodnie z definicją. 

Poniższy fragment kodu definiuje metadane klastra:
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Krok 3. Dodawanie SQL Server maszyn wirtualnych do klastra
Dodanie do klastra pierwszej SQL Serverej maszyny wirtualnej spowoduje utworzenie klastra. Polecenie [AZ SQL VM Add-to-Group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) tworzy klaster o nazwie podaną wcześniej, instaluje rolę klastra na maszynach wirtualnych SQL Server i dodaje je do klastra. Kolejne zastosowania `az sql vm add-to-group` polecenia Dodaj więcej SQL Server maszyn wirtualnych do nowo utworzonego klastra. 

Poniższy fragment kodu tworzy klaster i dodaje do niego pierwszą SQL Server maszynę wirtualną: 

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
Użyj tego polecenia, aby dodać do klastra wszystkie inne SQL Server maszyny wirtualne. Zmodyfikuj tylko `-n` parametr dla nazwy maszyny wirtualnej SQL Server. 

## <a name="step-4-create-the-availability-group"></a>Krok 4. Tworzenie grupy dostępności
Ręcznie Utwórz grupę dostępności jak zwykle za pomocą [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)lub [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Nie* Twórz teraz odbiornika, ponieważ odbywa się to za pomocą interfejsu wiersza polecenia platformy Azure w poniższych sekcjach.  

## <a name="step-5-create-the-internal-load-balancer"></a>Krok 5. Tworzenie wewnętrznego modułu równoważenia obciążenia

Odbiornik grupy dostępności zawsze włączony wymaga wewnętrznego wystąpienia Azure Load Balancer. Wewnętrzny moduł równoważenia obciążenia udostępnia "przestawny" adres IP dla odbiornika grupy dostępności, który umożliwia szybsze przełączanie w tryb failover i ponowne łączenie. Jeśli SQL Server maszyny wirtualne w grupie dostępności są częścią tego samego zestawu dostępności, można użyć podstawowego modułu równoważenia obciążenia. W przeciwnym razie należy użyć standardowego modułu równoważenia obciążenia.  

> [!NOTE]
> Wewnętrzny moduł równoważenia obciążenia powinien znajdować się w tej samej sieci wirtualnej co SQL Server wystąpienia maszyn wirtualnych. 

Poniższy fragment kodu tworzy wewnętrzny moduł równoważenia obciążenia:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Zasób publicznego adresu IP dla każdej maszyny wirtualnej SQL Server powinien mieć standardową jednostkę SKU zgodną z usługą równoważenia obciążenia w warstwie Standardowa. Aby określić jednostkę SKU publicznego adresu IP maszyny wirtualnej, przejdź do pozycji **Grupa zasobów**, wybierz zasób **publicznego adresu ip** dla żądanej SQL Serverj maszyny wirtualnej i Znajdź wartość w obszarze **jednostka SKU** w okienku **Przegląd** .  

## <a name="step-6-create-the-availability-group-listener"></a>Krok 6. Tworzenie odbiornika grupy dostępności
Po ręcznym utworzeniu grupy dostępności odbiornik można utworzyć za pomocą polecenia [AZ SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

*Identyfikator zasobu podsieci* jest wartością `/subnets/<subnetname>` dołączoną do identyfikatora zasobu zasobu sieci wirtualnej. Aby zidentyfikować identyfikator zasobu podsieci:
   1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com). 
   1. Wybierz zasób sieci wirtualnej. 
   1. W okienku **Ustawienia** wybierz pozycję **Właściwości** . 
   1. Zidentyfikuj identyfikator zasobu dla sieci wirtualnej i Dołącz `/subnets/<subnetname>` go do końca, aby utworzyć identyfikator zasobu podsieci. Na przykład:
      - Identyfikator zasobu sieci wirtualnej to:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Nazwa podsieci:`default`
      - W związku z tym identyfikator zasobu podsieci:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Poniższy fragment kodu tworzy odbiornik grupy dostępności:

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

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Modyfikowanie liczby replik w grupie dostępności
Po wdrożeniu grupy dostępności do SQL Server maszyn wirtualnych hostowanych na platformie Azure dodaliśmy warstwę złożoności. Dostawca zasobów i Grupa maszyn wirtualnych teraz zarządzają zasobami. W związku z tym podczas dodawania lub usuwania replik w grupie dostępności jest dostępny dodatkowy krok aktualizowania metadanych odbiornika przy użyciu informacji o maszynach wirtualnych SQL Server. W przypadku modyfikowania liczby replik w grupie dostępności należy również użyć polecenia [AZ SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) , aby zaktualizować odbiornik przy użyciu metadanych maszyn wirtualnych SQL Server. 


### <a name="add-a-replica"></a>Dodaj replikę

Aby dodać nową replikę do grupy dostępności:

1. Dodaj maszynę wirtualną SQL Server do klastra:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Użyj SQL Server Management Studio, aby dodać wystąpienie SQL Server jako replikę w grupie dostępności.
1. Dodaj SQL Server metadanych maszyny wirtualnej do odbiornika:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Usuń replikę

Aby usunąć replikę z grupy dostępności:

1. Usuń replikę z grupy dostępności przy użyciu SQL Server Management Studio. 
1. Usuń SQL Server metadanych maszyny wirtualnej z odbiornika:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Usuń maszynę wirtualną SQL Server z klastra:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Usuń odbiornik grupy dostępności
Jeśli później będzie konieczne usunięcie odbiornika grupy dostępności skonfigurowanego za pomocą interfejsu wiersza polecenia platformy Azure, musisz przejść przez dostawcę zasobów maszyny wirtualnej SQL. Ponieważ odbiornik jest zarejestrowany za pośrednictwem dostawcy zasobów maszyny wirtualnej SQL, usunięcie go za pośrednictwem SQL Server Management Studio jest niewystarczające. 

Najlepszą metodą jest usunięcie go za pośrednictwem dostawcy zasobów maszyny wirtualnej SQL przy użyciu poniższego fragmentu kodu w interfejsie wiersza polecenia platformy Azure. Spowoduje to usunięcie metadanych odbiornika grupy dostępności z dostawcy zasobów maszyny wirtualnej SQL. Również fizycznie usuwa odbiornik z grupy dostępności. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Przegląd SQL Server maszyn wirtualnych](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Często zadawane pytania dotyczące SQL Server maszyn wirtualnych](frequently-asked-questions-faq.md)
* [Informacje o wersji dla maszyn wirtualnych SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Przełączanie modeli licencjonowania dla maszyny wirtualnej SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Przegląd zawsze dostępnych grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfiguracja wystąpienia serwera dla zawsze włączonych grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administrowanie grupą dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorowanie grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Omówienie instrukcji języka Transact-SQL dla zawsze dostępnych grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Omówienie poleceń cmdlet programu PowerShell dla zawsze dostępnych grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
