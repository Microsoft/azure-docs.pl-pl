---
title: Ultra disks for VMS — Azure Managed disks
description: Dowiedz się więcej o usłudze Ultra disks dla maszyn wirtualnych platformy Azure
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/16/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 43dac1692dd6ee4ed1ab67a9b18ca69738e0a0f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580507"
---
# <a name="using-azure-ultra-disks"></a>Korzystanie z usługi Azure Ultra disks

W tym artykule wyjaśniono, jak wdrożyć dysk i korzystać z niego, aby uzyskać informacje koncepcyjne na temat Ultra disks, zobacz, [jakie typy dysków są dostępne na platformie Azure?](disks-types.md#ultra-disk).

Usługa Azure Ultra disks oferuje wysoką przepływność, dużą liczbę IOPS i spójny magazyn dyskowy o małym opóźnieniu dla maszyn wirtualnych IaaS platformy Azure. Ta nowa oferta zapewnia najwyższą wydajność linii na tych samych poziomach dostępności co istniejące dyski. Jedną z głównych zalet funkcji Ultra disks jest możliwość dynamicznego zmieniania wydajności dysków SSD wraz z obciążeniami bez konieczności ponownego uruchamiania maszyn wirtualnych. Dyski w warstwie Ultra to rozwiązanie odpowiednie w przypadku obciążeń intensywnie korzystających z danych, takich jak platforma SAP HANA, bazy danych górnej warstwy i obciążenia z dużą liczbą transakcji.

## <a name="ga-scope-and-limitations"></a>Zakres i ograniczenia dotyczące GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Określanie rozmiaru maszyny wirtualnej i dostępności regionów

### <a name="vms-using-availability-zones"></a>Maszyny wirtualne korzystające ze stref dostępności

Aby korzystać z Ultra disks, należy określić, w której strefie dostępności znajduje się. Nie każdy region obsługuje wszystkie rozmiary maszyn wirtualnych za pomocą Ultra Disks. Aby określić, czy rozmiar regionu, strefy i maszyny wirtualnej obsługuje Ultra disks, Uruchom jedno z następujących poleceń, pamiętaj, aby najpierw zastąpić wartości **regionu**, **vmSize** i **subskrypcji** :

#### <a name="cli"></a>Interfejs wiersza polecenia

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
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

Usługi Ultra disks wdrożone w wybranych regionach muszą zostać wdrożone bez opcji nadmiarowości. Jednak nie każdy rozmiar dysku, który obsługuje Ultra disks, może znajdować się w tym regionie. Aby określić, które rozmiary dysków obsługują Ultra dyski, można użyć dowolnego z poniższych fragmentów kodu. Pamiętaj, aby najpierw zastąpić `vmSize` `subscription` wartości i:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Odpowiedź będzie wyglądać podobnie do następującej: `UltraSSDAvailable   True` wskazuje, czy rozmiar maszyny wirtualnej obsługuje Ultra dyski w tym regionie.

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


## <a name="deploy-an-ultra-disk"></a>Wdróż dysk Ultra

# <a name="portal"></a>[Portal](#tab/azure-portal)

W tej sekcji omówiono wdrażanie maszyny wirtualnej wyposażonej w dysk jako dysk danych. Przyjęto założenie, że wiesz już, jak wdrożyć maszynę wirtualną, jeśli nie, zobacz [Przewodnik Szybki Start: Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](./windows/quick-create-portal.md).

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do wdrożenia maszyny wirtualnej.
1. Pamiętaj o wybraniu [obsługiwanego rozmiaru i regionu maszyny wirtualnej](#ga-scope-and-limitations).
1. Wybierz pozycję **strefa dostępności** w obszarze **Opcje dostępności**.
1. Wypełnij pozostałe wpisy wybranym wyborem.
1. Wybierz pozycję **Dyski**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png" alt-text="Zrzut ekranu przedstawiający przepływ tworzenia maszyny wirtualnej, blok podstawy." lightbox="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png":::

1. W bloku dyski wybierz pozycję **tak** dla opcji **Włącz zgodność z dyskiem**.
1. Wybierz pozycję **Utwórz i Dołącz nowy dysk** , aby teraz dołączyć dysk Ultra.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-disk-enable.png" alt-text="Zrzut ekranu przedstawiający przepływ tworzenia maszyn wirtualnych, blok dysku, Ultra jest włączony, a następnie Tworzenie i dołączanie nowego dysku jest wyróżniony." :::

1. W bloku **Utwórz nowy dysk** wprowadź nazwę, a następnie wybierz pozycję **Zmień rozmiar**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-create-disk.png" alt-text="Zrzut ekranu przedstawiający tworzenie nowego bloku dysku, Zmień rozmiar wyróżniony.":::


1. Zmień **jednostkę SKU dysku** na **dysk Ultra Disk**.
1. Zmień wartości **niestandardowego rozmiaru dysku (GIB)**, operacji we **/wy** na dysku i **przepływności dysku** do wybranych przez siebie opcji.
1. Wybierz **przycisk OK** w obu blokach.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-select-ultra-disk-size.png" alt-text="Zrzut ekranu przedstawiający blok Wybieranie rozmiaru dysku, Ultra dysk wybrany dla typu magazynu, inne wyróżnione wartości.":::

1. Kontynuuj Wdrażanie maszyny wirtualnej, tak samo jak w przypadku wdrożenia innej maszyny wirtualnej.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Zapoznaj się z sekcją " [ga Scope and ograniczenia](#ga-scope-and-limitations) ", aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych.

Aby dołączyć dysk Ultra, należy utworzyć maszynę wirtualną, która może korzystać z usługi Ultra Disks.

Zastąp lub ustaw **$VMName**, **$rgName**, **$diskname**, **$Location**, **$Password**, **$User** zmienne z własnymi wartościami. Ustaw **$Zone**  na wartość strefy dostępności uzyskaną od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć maszynę wirtualną o niezwykle włączonej:

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Zapoznaj się z sekcją " [ga Scope and ograniczenia](#ga-scope-and-limitations) ", aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych.

Aby korzystać z usługi Ultra disks, należy utworzyć maszynę wirtualną, która może korzystać z usługi Ultra Disks. Zastąp lub Ustaw zmienne **$resourcegroup** i **$vmName** własnymi wartościami. Ustaw **$Zone** na wartość strefy dostępności uzyskaną od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie [New-AzVm](/powershell/module/az.compute/new-azvm) , aby utworzyć maszynę wirtualną z funkcją Ultra Enabled:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>Tworzenie i dołączanie dysku

Po wdrożeniu maszyny wirtualnej możesz utworzyć i dołączyć do niej dysk, używając następującego skryptu:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
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
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---

## <a name="deploy-an-ultra-disk---512-byte-sector-size"></a>Wdróż dysk o rozmiarze Ultra disk – 512 bajtów

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się do [Azure Portal](https://portal.azure.com/), a następnie wyszukaj i wybierz pozycję **dyski**.
1. Wybierz pozycję **+ Nowy** , aby utworzyć nowy dysk.
1. Wybierz region obsługujący Ultra disks i wybierz strefę dostępności, a następnie wypełnij pozostałe wartości.
1. Wybierz pozycję **Zmień rozmiar**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/create-managed-disk-basics-workflow.png" alt-text="Zrzut ekranu przedstawiający blok Utwórz dysk, region, strefę dostępności i rozmiar wyróżniony.":::

1. W obszarze **jednostka SKU dysku** wybierz opcję **Ultra Disk**, a następnie wypełnij wartości żądanej wydajności i wybierz **przycisk OK**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-disk-size-ultra.png" alt-text="Zrzut ekranu przedstawiający tworzenie Ultra Disk.":::

1. W bloku **podstawowe** wybierz kartę **Zaawansowane** .
1. Wybierz **512** dla **rozmiaru sektora logicznego**, a następnie wybierz pozycję **Recenzja + Utwórz**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-different-sector-size-ultra.png" alt-text="Zrzut ekranu przedstawiający selektor zmiany rozmiaru sektora logicznego Ultra Disk na 512.":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Zapoznaj się z sekcją " [ga Scope and ograniczenia](#ga-scope-and-limitations) ", aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych.

Aby dołączyć dysk Ultra, należy utworzyć maszynę wirtualną, która może korzystać z usługi Ultra Disks.

Zastąp lub ustaw **$VMName**, **$rgName**, **$diskname**, **$Location**, **$Password**, **$User** zmienne z własnymi wartościami. Ustaw **$Zone**  na wartość strefy dostępności uzyskaną od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć maszynę wirtualną z dyskiem o rozmiarach o rozmiarze 512 bajtów:

```azurecli
#create an ultra disk with 512 sector size
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400 --logical-sector-size 512
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Zapoznaj się z sekcją " [ga Scope and ograniczenia](#ga-scope-and-limitations) ", aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych.

Aby korzystać z usługi Ultra disks, należy utworzyć maszynę wirtualną, która może korzystać z usługi Ultra Disks. Zastąp lub Ustaw zmienne **$resourcegroup** i **$vmName** własnymi wartościami. Ustaw **$Zone** na wartość strefy dostępności uzyskaną od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie [New-AzVm](/powershell/module/az.compute/new-azvm) , aby utworzyć maszynę wirtualną z funkcją Ultra Enabled:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

Aby utworzyć i dołączyć dysk Ultra z rozmiarem sektora 512 bajtów, można użyć następującego skryptu:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-LogicalSectorSize 512 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
---
## <a name="attach-an-ultra-disk"></a>Dołącz dysk Ultra

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może korzystać z usługi Ultra disks, można użyć funkcji Ultra disks bez konieczności tworzenia nowej maszyny wirtualnej. Przez włączenie funkcji Ultra disks na istniejącej maszynie wirtualnej, a następnie dołączenie ich jako dysków danych. Aby włączyć zgodność z dyskiem Ultra, należy zatrzymać maszynę wirtualną. Po zatrzymaniu maszyny wirtualnej możesz włączyć zgodność, a następnie ponownie uruchomić maszynę wirtualną. Po włączeniu zgodności możesz dołączyć Ultra Disk:

1. Przejdź do maszyny wirtualnej i zatrzymaj ją, poczekaj na jej cofnięcie.
1. Po cofnięciu przydziału maszyny wirtualnej wybierz pozycję **dyski**.
1. Wybierz **dodatkowe ustawienia**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-disk-additional-settings.png" alt-text="Zrzut ekranu przedstawiający blok dysku, dodatkowe ustawienia wyróżnione.":::

1. Wybierz pozycję **tak** dla opcji **Włącz zgodność z dyskiem**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Zrzut ekranu przedstawiający opcję Włącz zgodność z dyskiem.":::

1. Wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Utwórz i Dołącz nowy dysk** i wprowadź nazwę nowego dysku.
1. W obszarze **Typ magazynu** wybierz opcję **Ultra Disk**.
1. Zmień wartości **size (GIB)**, maks. **IOPS** i **maksymalną przepływność** na wybrane przez siebie opcje.
1. Po powrocie do bloku dysku wybierz pozycję **Zapisz**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-create-ultra-disk-existing-vm.png" alt-text="Zrzut ekranu przedstawiający blok dysku, dodając nowy dysk.":::

1. Uruchom ponownie maszynę wirtualną.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może korzystać z usługi Ultra disks, można użyć funkcji Ultra disks bez konieczności tworzenia nowej maszyny wirtualnej.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>Włącz zgodność z dyskiem twardym w ramach istniejącej maszyny wirtualnej — interfejs wiersza polecenia

Jeśli maszyna wirtualna spełnia wymagania opisane w temacie [ga zakres i ograniczenia](#ga-scope-and-limitations) i znajduje się w [odpowiedniej strefie dla Twojego konta](#determine-vm-size-and-region-availability), możesz włączyć zgodność z dyskami w maszynie wirtualnej.

Aby włączyć zgodność z dyskiem Ultra, należy zatrzymać maszynę wirtualną. Po zatrzymaniu maszyny wirtualnej możesz włączyć zgodność, a następnie ponownie uruchomić maszynę wirtualną. Po włączeniu zgodności możesz dołączyć Ultra Disk:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Tworzenie Ultra Disk — CLI

Teraz, gdy masz maszynę wirtualną, która może dołączać Ultra disks, możesz utworzyć i dołączyć do niej dysk.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

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

### <a name="attach-the-disk---cli"></a>Dołącz dysk — interfejs wiersza polecenia

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może korzystać z usługi Ultra disks, można użyć funkcji Ultra disks bez konieczności tworzenia nowej maszyny wirtualnej.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>Włączanie zapewnienia zgodności z dyskiem twardym na istniejącej maszynie wirtualnej — PowerShell

Jeśli maszyna wirtualna spełnia wymagania opisane w temacie [ga zakres i ograniczenia](#ga-scope-and-limitations) i znajduje się w [odpowiedniej strefie dla Twojego konta](#determine-vm-size-and-region-availability), możesz włączyć zgodność z dyskami w maszynie wirtualnej.

Aby włączyć zgodność z dyskiem Ultra, należy zatrzymać maszynę wirtualną. Po zatrzymaniu maszyny wirtualnej możesz włączyć zgodność, a następnie ponownie uruchomić maszynę wirtualną. Po włączeniu zgodności możesz dołączyć Ultra Disk:

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Tworzenie i dołączanie Ultra Disk — PowerShell

Teraz, gdy masz maszynę wirtualną, która może korzystać z usługi Ultra disks, możesz utworzyć i dołączyć do niej dysk:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
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
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>Dostosowywanie wydajności Ultra Disk

# <a name="portal"></a>[Portal](#tab/azure-portal)

Funkcja Ultra disks oferuje unikatową funkcję, która pozwala na dostosowanie ich wydajności. Możesz wprowadzić te korekty z Azure Portal na dyskach.

1. Przejdź do maszyny wirtualnej i wybierz pozycję **dyski**.
1. Wybierz dysk Ultra, dla którego chcesz zmodyfikować wydajność.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-ultra-disk-to-modify.png" alt-text="Zrzut ekranu przedstawiający blok dysków na maszynie wirtualnej jest wyróżniony.":::

1. Wybierz pozycję **rozmiar i wydajność** , a następnie wprowadź modyfikacje.
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/modify-ultra-disk-performance.png" alt-text="Zostanie wyróżniony zrzut ekranu przedstawiający blok konfiguracji na dysku typu Ultra, rozmiar dysku, liczba operacji we/wy na sekundę i przepływność.":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Funkcja Ultra disks oferuje unikatową funkcję umożliwiającą dostosowanie ich wydajności, następujące polecenie przedstawia sposób korzystania z tej funkcji:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Dostosowywanie wydajności Ultra Disk przy użyciu programu PowerShell

Funkcja Ultra disks ma unikatową funkcję, która pozwala na dostosowanie ich wydajności, ale następujące polecenie umożliwia dostosowanie wydajności bez konieczności odłączania dysku:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>Następne kroki

- [Użyj usługi Azure Ultra disks w usłudze Azure Kubernetes Service (wersja zapoznawcza)](../aks/use-ultra-disks.md).
- [Migruj dysk dziennika do postaci Ultra Disk](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md).
