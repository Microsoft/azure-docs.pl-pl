---
title: Tworzenie FCI przy użyciu dysków udostępnionych platformy Azure
description: Użyj usługi Azure Shared disks, aby utworzyć wystąpienie klastra trybu failover (FCI) z SQL Server na platformie Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: 6a000daa7d9e2aa93e68844e8aec5aa168c9fa60
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592419"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Tworzenie FCI przy użyciu dysków udostępnionych platformy Azure (SQL Server na maszynach wirtualnych platformy Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano sposób tworzenia wystąpienia klastra trybu failover (FCI) przy użyciu dysków udostępnionych platformy Azure z SQL Server na platformie Azure Virtual Machines. 

Aby dowiedzieć się więcej, zobacz Omówienie [FCI z SQL Server na maszynach wirtualnych platformy Azure](failover-cluster-instance-overview.md) i [najlepszych rozwiązaniach klastra](hadr-cluster-best-practices.md). 


## <a name="prerequisites"></a>Wymagania wstępne 

Przed wykonaniem instrukcji przedstawionych w tym artykule należy posiadać następujące czynności:

- Subskrypcja platformy Azure. Zacznij korzystać [bezpłatnie](https://azure.microsoft.com/free/). 
- [Co najmniej dwie maszyny wirtualne z systemem Windows Azure](failover-cluster-instance-prepare-vm.md). [Zestawy dostępności](../../../virtual-machines/windows/tutorial-availability-sets.md) i [grupy umieszczania zbliżeniowe](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) (PPGs) obsługiwane dla stref SSD w warstwie Premium i [dostępności](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) są obsługiwane dla Ultra Disks. Jeśli używasz PPG, wszystkie węzły muszą istnieć w tej samej grupie.
- Konto, które ma uprawnienia do tworzenia obiektów zarówno na maszynach wirtualnych platformy Azure, jak i w Active Directory.
- Najnowsza wersja programu [PowerShell](/powershell/azure/install-az-ps). 


## <a name="add-azure-shared-disk"></a>Dodaj dysk udostępniony platformy Azure
Wdróż zarządzany dysk SSD w warstwie Premium z włączoną funkcją dysku udostępnionego. Ustaw, `maxShares` Aby **wyrównać liczbę węzłów klastra** w celu udostępnienia dysku we wszystkich węzłach FCI. 

Dodaj dysk udostępniony platformy Azure, wykonując następujące czynności: 


1. Zapisz następujący skrypt jako *SharedDiskConfig.jsw*: 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```


2. Uruchom *SharedDiskConfig.js* przy użyciu programu PowerShell: 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. Dla każdej maszyny wirtualnej zainicjuj dołączone dyski udostępnione jako tabelę partycji GUID (GPT) i sformatuj je jako nowy system plików (NTFS), uruchamiając następujące polecenie: 

    ```powershell
    $resourceGroup = "<your resource group name>"
    $location = "<region of your shared disk>"
    $ppgName = "<your proximity placement groups name>"
    $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
        -Name "<your VM node name>"
    $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
        -DiskName "<your shared disk name>"
    $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
        -CreateOption Attach -ManagedDiskId $dataDisk.Id `
        -Lun <available LUN - check disk setting of the VM>
    Update-AzVm -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="create-failover-cluster"></a>Utwórz klaster trybu failover

Aby utworzyć klaster trybu failover, potrzebne są:

- Nazwy maszyn wirtualnych, które staną się węzłami klastra.
- Nazwa klastra trybu failover.
- Adres IP klastra trybu failover. Można użyć adresu IP, który nie jest używany w tej samej sieci wirtualnej platformy Azure i podsieci co węzły klastra.


# <a name="windows-server-2012-2016"></a>[System Windows Server 2012-2016](#tab/windows2012)

Poniższy skrypt programu PowerShell tworzy klaster trybu failover. Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Poniższy skrypt programu PowerShell tworzy klaster trybu failover. Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Aby uzyskać więcej informacji, zobacz [klaster trybu failover: obiekt sieci klastra](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Konfigurowanie kworum

Skonfiguruj rozwiązanie kworum, które najlepiej odpowiada Twoim potrzebom biznesowym. Można skonfigurować [monitor dysku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), [monitor w chmurze](/windows-server/failover-clustering/deploy-cloud-witness)lub [monitor udostępniania plików](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Aby uzyskać więcej informacji, zobacz [kworum z maszynami wirtualnymi SQL Server](hadr-cluster-best-practices.md#quorum). 

## <a name="validate-cluster"></a>Weryfikuj klaster
Sprawdź poprawność klastra w interfejsie użytkownika lub za pomocą programu PowerShell.

Aby sprawdzić poprawność klastra przy użyciu interfejsu użytkownika, wykonaj następujące czynności na jednej z maszyn wirtualnych:

1. W obszarze **Menedżer serwera** wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Menedżer klastra trybu failover**.
1. W obszarze **Menedżer klastra trybu failover** wybierz pozycję **Akcja**, a następnie wybierz pozycję **Weryfikuj konfigurację**.
1. Wybierz pozycję **Dalej**.
1. W obszarze **Wybierz serwery lub klaster** wprowadź nazwy obu maszyn wirtualnych.
1. W obszarze **opcje testowania** wybierz opcję **Uruchom tylko wybrane testy**. 
1. Wybierz pozycję **Dalej**.
1. W obszarze **wybór testu** zaznacz wszystkie testy *poza* **magazynem**

## <a name="test-cluster-failover"></a>Testowanie trybu failover klastra

Przetestuj tryb failover klastra. W **Menedżer klastra trybu failover** kliknij prawym przyciskiem myszy klaster, wybierz pozycję **więcej akcji**  >  **Przenieś zasób klastra podstawowego**  >  **Wybierz węzeł**, a następnie wybierz inny węzeł klastra. Przenieś zasób podstawowego klastra do każdego węzła klastra, a następnie przenieś go z powrotem do węzła podstawowego. Jeśli możesz pomyślnie przenieść klaster do każdego węzła, możesz zainstalować SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testowanie trybu failover klastra przez przeniesienie zasobu podstawowego do innych węzłów":::

## <a name="create-sql-server-fci"></a>Utwórz SQL Server FCI

Po skonfigurowaniu klastra trybu failover i wszystkich składników klastra, w tym magazynu, można utworzyć SQL Server FCI.

1. Połącz się z pierwszą maszyną wirtualną przy użyciu Remote Desktop Protocol (RDP).

1. W **Menedżer klastra trybu failover** upewnij się, że wszystkie podstawowe zasoby klastra znajdują się na pierwszej maszynie wirtualnej. W razie potrzeby Przenieś wszystkie zasoby na tę maszynę wirtualną.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów portalu Azure Marketplace, nośnik znajduje się w lokalizacji `C:\SQLServer_<version number>_Full` . 

1. Wybierz pozycję **Konfiguracja**.

1. W **SQL Server centrum instalacji** wybierz opcję **Instalacja**.

1. Wybierz pozycję **nowa SQL Server Instalacja klastra trybu failover**. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server FCI.

Katalogi danych FCI muszą znajdować się na udostępnionych dyskach platformy Azure. 

1. Po wykonaniu instrukcji w Kreatorze Instalator zainstaluje SQL Server FCI na pierwszym węźle.

1. Po zainstalowaniu przez Instalatora FCI na pierwszym węźle Nawiąż połączenie z drugim węzłem przy użyciu protokołu RDP.

1. Otwórz **centrum instalacji programu SQL Server**, a następnie wybierz pozycję **Instalacja**.

1. Wybierz pozycję **Dodaj węzeł do klastra trybu failover SQL Server**. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server i dodać serwer do FCI.

   >[!NOTE]
   >W przypadku korzystania z obrazu galerii portalu Azure Marketplace zawierającego SQL Server narzędzia SQL Server zostały dołączone do obrazu. Jeśli nie korzystasz z jednego z tych obrazów, zainstaluj SQL Server narzędzia osobno. Aby uzyskać więcej informacji, zobacz [pobieranie SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >

## <a name="register-with-the-sql-vm-rp"></a>Rejestrowanie w ramach jednostki odzyskiwania maszyny wirtualnej SQL

Aby zarządzać maszyną wirtualną SQL Server z poziomu portalu, zarejestruj ją za pomocą rozszerzenia programu SQL IaaS Agent (RP) w [trybie zarządzania uproszczonego](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), obecnie jedynym trybem obsługiwanym z FCI i SQL Server na maszynach wirtualnych platformy Azure. 


Rejestrowanie maszyny wirtualnej SQL Server w trybie uproszczonym przy użyciu programu PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurowanie łączności 

Aby skierować ruch odpowiednio do bieżącego węzła podstawowego, należy skonfigurować opcję łączności, która jest odpowiednia dla danego środowiska. Można utworzyć [moduł równoważenia obciążenia platformy Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) lub, jeśli używasz SQL Server 2019 zastosujesz pakietu CU2 (lub nowszego) i systemu Windows Server 2016 (lub nowszego), zamiast tego możesz użyć funkcji [rozproszonej nazwy sieciowej](failover-cluster-instance-distributed-network-name-dnn-configure.md) . 

## <a name="limitations"></a>Ograniczenia

- Obsługiwane jest tylko rejestrowanie przy użyciu rozszerzenia agenta SQL IaaS w [trybie uproszczonego zarządzania](sql-server-iaas-agent-extension-automate-management.md#management-modes) .

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, skonfiguruj łączność z FCI przy użyciu [nazwy sieci wirtualnej i modułu równoważenia obciążenia platformy Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) lub [nazwy sieci rozproszonej (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Jeśli udostępnione dyski platformy Azure nie są odpowiednim rozwiązaniem magazynu FCI, rozważ utworzenie FCI przy użyciu [udziałów plików Premium](failover-cluster-instance-premium-file-share-manually-configure.md) lub [bezpośrednie miejsca do magazynowania](failover-cluster-instance-storage-spaces-direct-manually-configure.md) zamiast tego. 

Aby dowiedzieć się więcej, zobacz Omówienie [FCI z SQL Server na maszynach wirtualnych platformy Azure](failover-cluster-instance-overview.md) i [najlepszych rozwiązaniach dotyczących konfiguracji klastra](hadr-cluster-best-practices.md).

Aby uzyskać więcej informacji, zobacz: 
- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server wystąpienia klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)