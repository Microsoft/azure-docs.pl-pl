---
title: Wdrażanie Virtual Machines usługi Azure spot przy użyciu programu PowerShell
description: Dowiedz się, jak za pomocą Azure PowerShell wdrożyć Virtual Machines usługi Azure Spot, aby zaoszczędzić na kosztach.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802747"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Wdróż Virtual Machines w miejscu na platformie Azure przy użyciu Azure PowerShell


Korzystanie z [usługi Azure Spot Virtual Machines](../spot-vms.md) umożliwia korzystanie z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wyłączy Virtual Machines na platformie Azure. Z tego względu Virtual Machines na platformie Azure są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Cennik usługi Azure Virtual Machines w miejscu to zmienna, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [Virtual Machines platformy Azure — cennik](../spot-vms.md#pricing).

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej usługi Azure Spot można ustawić w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.98765` Cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz element spotVM przy użyciu polecenia [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) , aby utworzyć konfigurację. Dołącz `-Priority Spot` i ustaw `-MaxPrice` jako:
- `-1` Dlatego, że maszyna wirtualna nie zostanie wykluczona na podstawie ceny.
- kwota dolara (do 5 cyfr). Na przykład `-MaxPrice .98765` oznacza, że zostanie cofnięta alokacja maszyny wirtualnej, gdy cena za spotVM wyniesie około USD. 98765 na godzinę.


Ten przykład tworzy spotVM, które nie zostaną cofnięte w oparciu o Cennik (tylko wtedy, gdy platforma Azure wymaga powrotu do tyłu pojemności). Zasada wykluczenia jest ustawiona na cofnięcie alokacji maszyny wirtualnej, dzięki czemu można ją uruchomić ponownie w późniejszym czasie. Jeśli chcesz usunąć maszynę wirtualną i dysk źródłowy podczas wykluczania maszyny wirtualnej, ustaw na wartość `-EvictionPolicy` `Delete` w `New-AzVMConfig` .


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Po utworzeniu maszyny wirtualnej można wykonać zapytanie, aby zobaczyć maksymalną cenę dla wszystkich maszyn wirtualnych w grupie zasobów.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Symulowanie wykluczenia

Możesz symulować wykluczenie maszyny wirtualnej platformy Azure w środowisku REST, programie PowerShell lub interfejsie wiersza polecenia, aby sprawdzić, jak dobrze aplikacja reaguje na nagłe wykluczenie.

W większości przypadków należy użyć interfejsu API REST [Virtual Machines — Symuluj wykluczenie](/rest/api/compute/virtualmachines/simulateeviction) , aby pomóc w zautomatyzowanym testowaniu aplikacji. W przypadku protokołu REST `Response Code: 204` oznacza to, że symulowane wykluczenie zakończyło się pomyślnie. Można połączyć symulowane wykluczenia z [zaplanowaną usługą zdarzeń](scheduled-events.md), aby zautomatyzować, w jaki sposób aplikacja będzie odpowiadać po wykluczeniu maszyny wirtualnej.

Aby zobaczyć zaplanowane zdarzenia w działaniu, Obejrzyj [piątek z platformy Azure, korzystając z usługi azure Scheduled Events w celu przygotowania do konserwacji maszyn wirtualnych](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Szybki test

Aby uzyskać szybki test pokazujący, jak będzie działał symulowane wykluczenie, przejdźmy do zapytania dotyczącego usługi zdarzeń zaplanowanych, aby zobaczyć, co się dzieje w przypadku symulowania wykluczenia przy użyciu programu PowerShell.

Zaplanowana usługa zdarzeń jest włączona dla Twojej usługi podczas pierwszego żądania zdarzeń. 

Zdalnie z maszyną wirtualną, a następnie otwórz wiersz polecenia. 

W wierszu polecenia na maszynie wirtualnej wpisz:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Ta pierwsza odpowiedź może potrwać do 2 minut. Od teraz powinny być wyświetlane dane wyjściowe niemal natychmiast.

Na komputerze, na którym zainstalowano moduł AZ PowerShell (taki jak komputer lokalny), Symuluj wykluczenie przy użyciu polecenia [Set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). Zastąp własną nazwę grupy zasobów i nazwę maszyny wirtualnej. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

Dane wyjściowe odpowiedzi będą mieć, `Status: Succeeded` Jeśli żądanie zostało wykonane pomyślnie.

Szybko Wróć do zdalnego połączenia z maszyną wirtualną, a następnie ponownie wykonaj zapytanie o punkt końcowy Scheduled Events. Powtórz następujące polecenie do momentu uzyskania danych wyjściowych, które zawierają więcej informacji:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Gdy usługa zaplanowanego zdarzenia otrzymuje powiadomienie o wykluczeniu, otrzymasz odpowiedź podobną do:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Można je zobaczyć `"EventType":"Preempt"` , a zasób jest zasobem maszyny wirtualnej `"Resources":["myspotvm"]` . 

Możesz również sprawdzić, kiedy maszyna wirtualna zostanie wykluczona, sprawdzając `"NotBefore"` wartość. Maszyna wirtualna nie zostanie wykluczona przed upływem czasu określonego w programie `NotBefore` , więc jest to okno aplikacji do bezpiecznego zamknięcia.


## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć maszynę wirtualną platformy Azure w miejscu przy użyciu [interfejsu wiersza polecenia platformy Azure](../linux/spot-cli.md), [portalu](../spot-portal.md) lub [szablonu](../linux/spot-template.md).

Zapoznaj się z bieżącymi informacjami o cenach przy użyciu [interfejsu API cen detalicznych platformy Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) , aby uzyskać informacje o cenach maszyn wirtualnych platformy Azure. Elementy `meterName` i `skuName` będą zawierać `Spot` .

Jeśli wystąpi błąd, zobacz [kody błędów](../error-codes-spot.md).
