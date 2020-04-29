---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008647"
---
Usługa Azure Ultra disks oferuje wysoką przepływność, dużą liczbę IOPS i spójny magazyn dyskowy o małym opóźnieniu dla maszyn wirtualnych IaaS platformy Azure. Ta nowa oferta zapewnia najwyższą wydajność linii na tych samych poziomach dostępności co istniejące dyski. Jedną z głównych zalet funkcji Ultra disks jest możliwość dynamicznego zmieniania wydajności dysków SSD wraz z obciążeniami bez konieczności ponownego uruchamiania maszyn wirtualnych. Dyski w warstwie Ultra to rozwiązanie odpowiednie w przypadku obciążeń intensywnie korzystających z danych, takich jak platforma SAP HANA, bazy danych górnej warstwy i obciążenia z dużą liczbą transakcji.

## <a name="ga-scope-and-limitations"></a>Zakres i ograniczenia dotyczące GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Określanie rozmiaru maszyny wirtualnej i dostępności regionów

### <a name="vms-using-availability-zones"></a>Maszyny wirtualne korzystające ze stref dostępności

Aby korzystać z Ultra disks, należy określić, w której strefie dostępności znajduje się. Nie każdy region obsługuje wszystkie rozmiary maszyn wirtualnych za pomocą Ultra Disks. Aby określić, czy rozmiar regionu, strefy i maszyny wirtualnej obsługuje Ultra disks, Uruchom jedno z następujących poleceń, pamiętaj, aby najpierw zastąpić wartości **regionu**, **vmSize**i **subskrypcji** :

#### <a name="cli"></a>Interfejs wiersza polecenia

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Odpowiedź będzie wyglądać podobnie do poniższego formularza, gdzie X jest strefą używaną do wdrożenia w wybranym regionie. Wartość X może być 1, 2 lub 3.

Zachowywanie wartości **strefy** reprezentuje strefę dostępności i będzie potrzebna do wdrożenia Ultra Disk.

|ResourceType  |Nazwa  |Lokalizacja  |Strefy  |Ograniczenie  |Możliwość  |Wartość  |
|---------|---------|---------|---------|---------|---------|---------|
|dysku     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Jeśli nie ma odpowiedzi z polecenia, wybrany rozmiar maszyny wirtualnej nie jest obsługiwany w przypadku Ultra dysków w wybranym regionie.

Teraz, gdy znasz strefę do wdrożenia, wykonaj kroki wdrażania opisane w tym artykule, aby wdrożyć maszynę wirtualną z dołączonym dyskiem Ultra lub dołączyć dysk Ultra do istniejącej maszyny wirtualnej.

### <a name="vms-with-no-redundancy-options"></a>Maszyny wirtualne bez opcji nadmiarowości

Usługi Ultra disks wdrożone w regionie zachodnie stany USA muszą zostać wdrożone bez żadnych opcji nadmiarowości. Jednak nie każdy rozmiar dysku, który obsługuje Ultra disks, może znajdować się w tym regionie. Aby określić, które z nich znajdowały się w regionie zachodnie stany USA, można użyć dowolnego z poniższych fragmentów kodu. Pamiętaj, `vmSize` aby najpierw zastąpić wartości `subscription` i:

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Odpowiedź będzie wyglądać podobnie do następującej: wskazuje, `UltraSSDAvailable   True` czy rozmiar maszyny wirtualnej obsługuje Ultra dyski w tym regionie.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Wdróż dysk Ultra przy użyciu Azure Resource Manager

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Listę obsługiwanych rozmiarów maszyn wirtualnych można znaleźć w sekcji [ga zakres i ograniczenia](#ga-scope-and-limitations) .

Jeśli chcesz utworzyć maszynę wirtualną z wieloma dyskami, zapoznaj się z przykładem [Tworzenie maszyny wirtualnej z wieloma dyskami](https://aka.ms/ultradiskArmTemplate).

Jeśli zamierzasz użyć własnego szablonu, upewnij się, że **apiVersion** dla `Microsoft.Compute/virtualMachines` i `Microsoft.Compute/Disks` jest ustawiony jako `2018-06-01` (lub nowszy).

Ustaw jednostkę SKU dysku na **UltraSSD_LRS**, a następnie ustaw wartość dyskową, liczbę operacji we/wy na sekundę, strefę dostępności i przepływność w MB/s, aby utworzyć dysk o najwyższej wydajności.

Po aprowizacji maszyny wirtualnej można podzielić na partycje i sformatować dyski danych i skonfigurować je do obciążeń.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Wdróż dysk Ultra przy użyciu Azure Portal

W tej sekcji omówiono wdrażanie maszyny wirtualnej wyposażonej w dysk jako dysk danych. Przyjęto założenie, że wiesz już, jak wdrożyć maszynę wirtualną, jeśli nie, zobacz [Przewodnik Szybki Start: Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../articles/virtual-machines/windows/quick-create-portal.md).

- Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do wdrożenia maszyny wirtualnej.
- Pamiętaj o wybraniu [obsługiwanego rozmiaru i regionu maszyny wirtualnej](#ga-scope-and-limitations).
- Wybierz pozycję **strefa dostępności** w obszarze **Opcje dostępności**.
- Wypełnij pozostałe wpisy wybranym wyborem.
- Wybierz pozycję **Dyski**.

![Create-Ultra-Disk-Enabled-VM. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- W bloku dyski wybierz pozycję **tak** dla opcji **Włącz zgodność z dyskiem**.
- Wybierz pozycję **Utwórz i Dołącz nowy dysk** , aby teraz dołączyć dysk Ultra.

![Enable-and-Attach-Ultra-Disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- W bloku **Utwórz nowy dysk** wprowadź nazwę, a następnie wybierz pozycję **Zmień rozmiar**.
- Zmień **Typ konta** na **Ultra Disk**.
- Zmień wartości **niestandardowego rozmiaru dysku (GIB)**, operacji we **/wy**na dysku i **przepływności dysku** do wybranych przez siebie opcji.
- Wybierz **przycisk OK** w obu blokach.
- Kontynuuj Wdrażanie maszyny wirtualnej, tak samo jak w przypadku wdrożenia innej maszyny wirtualnej.

![Create-Ultra-Disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Dołącz dysk Ultra przy użyciu Azure Portal

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może korzystać z usługi Ultra disks, można użyć funkcji Ultra disks bez konieczności tworzenia nowej maszyny wirtualnej. Przez włączenie funkcji Ultra disks na istniejącej maszynie wirtualnej, a następnie dołączenie ich jako dysków danych.

- Przejdź do maszyny wirtualnej i wybierz pozycję **dyski**.
- Wybierz pozycję **Edit** (Edytuj).

![Options-Selector-Ultra-Disks. png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Wybierz pozycję **tak** dla opcji **Włącz zgodność z dyskiem**.

![Ultra-Options-Yes-Enable. png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Wybierz pozycję **Zapisz**.
- Wybierz pozycję **Dodaj dysk danych** , a **następnie na liście** rozwijanej wybierz pozycję **Utwórz dysk**.

![Create-and-Attach-New-Ultra-Disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Wprowadź nazwę nowego dysku, a następnie wybierz pozycję **Zmień rozmiar**.
- Zmień **Typ konta** na **Ultra Disk**.
- Zmień wartości **niestandardowego rozmiaru dysku (GIB)**, operacji we **/wy**na dysku i **przepływności dysku** do wybranych przez siebie opcji.
- Wybierz przycisk **OK** , a następnie wybierz pozycję **Utwórz**.

![Making-a-New-Ultra-Disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Po powrocie do bloku dysku wybierz pozycję **Zapisz**.

![Saving-and-Attaching-New-Ultra-Disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Dostosuj wydajność Ultra Disk przy użyciu Azure Portal

Funkcja Ultra disks oferuje unikatową funkcję, która pozwala na dostosowanie ich wydajności. Możesz wprowadzić te korekty z Azure Portal na dyskach.

- Przejdź do maszyny wirtualnej i wybierz pozycję **dyski**.
- Wybierz dysk Ultra, dla którego chcesz zmodyfikować wydajność.

![Selecting-Ultra-Disk-to-Modify. png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Wybierz pozycję **Konfiguracja** , a następnie wprowadź modyfikacje.
- Wybierz pozycję **Zapisz**.

![Configuring-Ultra-Disk-Performance-and-size. png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Wdrażanie Ultra Disk przy użyciu interfejsu wiersza polecenia

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Zapoznaj się z sekcją " [ga Scope and ograniczenia](#ga-scope-and-limitations) ", aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych.

Aby dołączyć dysk Ultra, należy utworzyć maszynę wirtualną, która może korzystać z usługi Ultra Disks.

Zastąp lub ustaw **$VMName**, **$rgName**, **$diskname**, **$Location**, **$Password**, **$User** zmienne z własnymi wartościami. Ustaw **$Zone** na wartość strefy dostępności uzyskaną od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć maszynę wirtualną o niezwykle włączonej:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Włącz zgodność z dyskiem twardym na istniejącej maszynie wirtualnej

Jeśli maszyna wirtualna spełnia wymagania opisane w temacie [ga zakres i ograniczenia](#ga-scope-and-limitations) i znajduje się w [odpowiedniej strefie dla Twojego konta](#determine-vm-size-and-region-availability), możesz włączyć zgodność z dyskami w maszynie wirtualnej.

Aby włączyć zgodność z dyskiem Ultra, należy zatrzymać maszynę wirtualną. Po zatrzymaniu maszyny wirtualnej możesz włączyć zgodność, dołączyć dysk Ultra, a następnie ponownie uruchomić maszynę wirtualną:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Tworzenie Ultra Disk przy użyciu interfejsu wiersza polecenia

Teraz, gdy masz maszynę wirtualną, która może dołączać Ultra disks, możesz utworzyć i dołączyć do niej dysk.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Dołączanie dysku Ultra do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może korzystać z usługi Ultra disks, można użyć funkcji Ultra disks bez konieczności tworzenia nowej maszyny wirtualnej.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Dostosowywanie wydajności Ultra Disk przy użyciu interfejsu wiersza polecenia

Funkcja Ultra disks oferuje unikatową funkcję umożliwiającą dostosowanie ich wydajności, następujące polecenie przedstawia sposób korzystania z tej funkcji:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Wdrażanie programu Ultra Disk przy użyciu programu PowerShell

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Zapoznaj się z sekcją " [ga Scope and ograniczenia](#ga-scope-and-limitations) ", aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych.

Aby korzystać z usługi Ultra disks, należy utworzyć maszynę wirtualną, która może korzystać z usługi Ultra Disks. Zastąp lub Ustaw zmienne **$resourcegroup** i **$vmName** własnymi wartościami. Ustaw **$Zone** na wartość strefy dostępności uzyskaną od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie [New-AzVm](/powershell/module/az.compute/new-azvm) , aby utworzyć maszynę wirtualną z funkcją Ultra Enabled:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Włącz zgodność z dyskiem twardym na istniejącej maszynie wirtualnej

Jeśli maszyna wirtualna spełnia wymagania opisane w temacie [ga zakres i ograniczenia](#ga-scope-and-limitations) i znajduje się w [odpowiedniej strefie dla Twojego konta](#determine-vm-size-and-region-availability), możesz włączyć zgodność z dyskami w maszynie wirtualnej.

Aby włączyć zgodność z dyskiem Ultra, należy zatrzymać maszynę wirtualną. Po zatrzymaniu maszyny wirtualnej możesz włączyć zgodność, dołączyć dysk Ultra, a następnie ponownie uruchomić maszynę wirtualną:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>Tworzenie Ultra Disk przy użyciu programu PowerShell

Teraz, gdy masz maszynę wirtualną, która może korzystać z usługi Ultra disks, możesz utworzyć i dołączyć do niej dysk:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Dołączanie dysku Ultra do maszyny wirtualnej przy użyciu programu PowerShell

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może korzystać z usługi Ultra disks, można użyć funkcji Ultra disks bez konieczności tworzenia nowej maszyny wirtualnej.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Dostosowywanie wydajności Ultra Disk przy użyciu programu PowerShell

Funkcja Ultra disks ma unikatową funkcję, która pozwala na dostosowanie ich wydajności, ale następujące polecenie umożliwia dostosowanie wydajności bez konieczności odłączania dysku:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```