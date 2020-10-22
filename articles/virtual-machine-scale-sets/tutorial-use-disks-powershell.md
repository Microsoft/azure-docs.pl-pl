---
title: Samouczek — Tworzenie i używanie dysków dla zestawów skalowania za pomocą Azure PowerShell
description: Dowiedz się, jak za pomocą programu Azure PowerShell utworzyć dyski funkcji Dyski zarządzane i używać ich razem z zestawem skalowania maszyn wirtualnych, na przykład dodawać, przygotowywać, wyświetlać i odłączać dyski.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 9e995e88b80bf14f9c7784f465bcd3d89d0bed65
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367962"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>Samouczek: tworzenie dysków i używanie ich z zestawem skalowania maszyn wirtualnych za pośrednictwem programu Azure PowerShell

Zestawy skalowania maszyn wirtualnych przechowują aplikacje, dane oraz systemy operacyjne wystąpień maszyn wirtualnych na dyskach. Ważne jest, aby podczas tworzenia zestawu skalowania i zarządzania nim wybrać taki rozmiar dysku i konfigurację, które odpowiadają oczekiwanemu obciążeniu. W tym samouczku omówiono tworzenie dysków maszyn wirtualnych i zarządzanie nimi. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski danych
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>Domyślne dyski platformy Azure
Podczas tworzenia lub skalowania zestawu skalowania do każdego wystąpienia maszyny wirtualnej są automatycznie dołączane dwa dyski. 

**Dysk systemu operacyjnego** — dyski systemu operacyjnego mogą mieć rozmiar do 2 TB i hostują system operacyjny wystąpienia maszyny wirtualnej. Domyślnie dysk sytemu operacyjnego ma etykietę */dev/sda*. Konfiguracja buforowania dysku systemu operacyjnego jest zoptymalizowana pod kątem wydajności systemu operacyjnego. Ze względu na tę konfigurację dysk sytemu operacyjnego **nie powinien** hostować aplikacji ani danych. Na potrzeby aplikacji i danych należy użyć dysków z danymi, które zostaną szczegółowo opisane w dalszej części tego artykułu. 

**Dysk tymczasowy** — dyski tymczasowe używają dysku SSD, który znajduje się na tym samym hoście platformy Azure co wystąpienie maszyny wirtualnej. Dyski te są wysoce wydajne i można przy ich użyciu wykonywać operacje takie jak przetwarzanie danych tymczasowych. Jednak jeśli wystąpienie maszyny wirtualnej zostanie przeniesione na nowy host, wszystkie dane przechowywane na dysku tymczasowym zostaną usunięte. Rozmiar dysku tymczasowego zależy od rozmiaru wystąpienia maszyny wirtualnej. Dyski tymczasowe mają etykietę */dev/sdb* oraz punkt instalacji */mnt*.

### <a name="temporary-disk-sizes"></a>Rozmiary dysków tymczasowych
| Typ | Typowe rozmiary | Maksymalny rozmiar dysku tymczasowego (GiB) |
|----|----|----|
| [Ogólnego przeznaczenia](../virtual-machines/sizes-general.md) | Seria A, B i D | 1600 |
| [Optymalizacja pod kątem obliczeń](../virtual-machines/sizes-compute.md) | Seria F | 576 |
| [Optymalizacja pod kątem pamięci](../virtual-machines/sizes-memory.md) | Seria D, E, G i M | 6144 |
| [Optymalizacja pod kątem magazynu](../virtual-machines/sizes-storage.md) | Seria L | 5630 |
| [Procesor GPU](../virtual-machines/sizes-gpu.md) | Seria N | 1440 |
| [Wysoka wydajność](../virtual-machines/sizes-hpc.md) | Seria A i H | 2000 |


## <a name="azure-data-disks"></a>Dyski z danymi platformy Azure
W przypadku konieczności instalowania aplikacji i przechowywania danych można dodać kolejne dyski z danymi. Dyski z danymi powinny być używane w sytuacji, gdy potrzebny jest trwały i dynamiczny magazyn danych. Każdy dysk z danymi ma maksymalną pojemność wynoszącą 4 TB. Liczba dysków z danymi, które można dołączyć, zależy od rozmiaru wystąpienia maszyny wirtualnej. Na każdy procesor wirtualny maszyny wirtualnej można dołączyć dwa dyski z danymi.

### <a name="max-data-disks-per-vm"></a>Maksymalna liczba dysków z danymi na maszynę wirtualną
| Typ | Typowe rozmiary | Maksymalna liczba dysków z danymi na maszynę wirtualną |
|----|----|----|
| [Ogólnego przeznaczenia](../virtual-machines/sizes-general.md) | Seria A, B i D | 64 |
| [Optymalizacja pod kątem obliczeń](../virtual-machines/sizes-compute.md) | Seria F | 64 |
| [Optymalizacja pod kątem pamięci](../virtual-machines/sizes-memory.md) | Seria D, E, G i M | 64 |
| [Optymalizacja pod kątem magazynu](../virtual-machines/sizes-storage.md) | Seria L | 64 |
| [Procesor GPU](../virtual-machines/sizes-gpu.md) | Seria N | 64 |
| [Wysoka wydajność](../virtual-machines/sizes-hpc.md) | Seria A i H | 64 |


## <a name="vm-disk-types"></a>Typy dysków maszyny wirtualnej
Na platformie Azure dostępne są dwa typy dysków.

### <a name="standard-disk"></a>Dysk w warstwie Standardowa
Bazujący na dyskach twardych (HDD) magazyn Standard Storage stanowi ekonomiczne i wydajne rozwiązanie. Dyski w warstwie Standardowa idealnie nadają się do ekonomicznej obsługi obciążeń tworzenia i testowania.

### <a name="premium-disk"></a>Dysk w warstwie Premium
W warstwie Premium są używane dyski o wysokiej wydajności i niskim opóźnieniu, oparte na technologii SSD. Te dyski są zalecane w przypadku maszyn wirtualnych, na których są uruchamiane obciążenia produkcyjne. Usługa Premium Storage obsługuje maszyny wirtualne z serii DS, DSv2, GS i FS. Podczas wybierania rozmiaru dysku wartość jest zaokrąglana do następnego typu. Jeśli na przykład rozmiar dysku wynosi mniej niż 128 GB, jest to dysk typu P10. Jeśli rozmiar dysku należy do zakresu od 129 GB do 512 GB, jest to dysk typu P20. Dyski o rozmiarze powyżej 512 GB są dyskami typu P30.

### <a name="premium-disk-performance"></a>Wydajność dysku w warstwie Premium
|Typ dysku magazynu Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Rozmiar dysku (zaokrąglony w górę) | 32 GB | 64 GB | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) |
| Maksymalna liczba operacji wejścia/wyjścia na sekundę na dysk | 120 | 240 | 500 | 2300 | 5000 | 7500 | 7500 |
Przepływność na dysk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

W powyższej tabeli podano maksymalną liczbę operacji wejścia/wyjścia na sekundę na dysk, ale wyższą wydajność można osiągnąć przez stosowanie wielu dysków z danymi. Na przykład maszyna wirtualna Standard_GS5 może osiągnąć maksymalnie 80 000 operacji we/wy na sekundę Aby uzyskać szczegółowe informacje na temat maksymalnej liczby operacji we/wy na sekundę na maszynę wirtualną, zobacz [Rozmiary maszyn wirtualnych z systemem Windows](../virtual-machines/sizes.md).


## <a name="create-and-attach-disks"></a>Tworzenie i dołączanie dysków
Dyski można tworzyć i dołączać podczas tworzenia zestawu skalowania lub w ramach modyfikacji istniejącego zestawu skalowania.

Począwszy od wersji interfejsu API `2019-07-01` , można ustawić rozmiar dysku systemu operacyjnego w zestawie skalowania maszyn wirtualnych za pomocą właściwości [obszarze storageprofile. OsDisk. diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) . Po zainicjowaniu obsługi może być konieczne rozwinięcie lub ponowne partycjonowanie dysku, aby wykorzystać całe miejsce. Dowiedz się więcej [na temat rozszerzania dysku w tym miejscu](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Dołączanie dysków podczas tworzenia zestawu skalowania
Utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzVmss](/powershell/module/az.compute/new-azvmss). Po wyświetleniu monitu podaj nazwę użytkownika i hasło dla wystąpień maszyn wirtualnych. Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985.

Dwa dyski są tworzone za pomocą parametru `-DataDiskSizeGb`. Pierwszy dysk ma rozmiar *64* GB, a drugi dysk to *128* GB. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania oraz wystąpień maszyn wirtualnych trwa kilka minut.

### <a name="attach-a-disk-to-existing-scale-set"></a>Dołączanie dysku do istniejącego zestawu skalowania
Dyski można także dołączać do istniejącego zestawu skalowania. Za pomocą polecenia [Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk) dodaj kolejny dysk do zestawu skalowania utworzonego w poprzednim kroku. W poniższym przykładzie do istniejącego zestawu skalowania jest dołączany dodatkowy dysk o rozmiarze *128* GB:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>Przygotowywanie dysków z danymi
Utworzone dyski, które zostały dołączone do wystąpień maszyn wirtualnych w zestawie skalowania, są niesformatowane. Należy je przygotować, zanim będzie można ich używać z danymi i aplikacjami. Aby przygotować dyski, musisz utworzyć partycję oraz system plików oraz je zainstalować.

Aby zautomatyzować ten proces w wielu wystąpieniach maszyn wirtualnych w zestawie skalowania, możesz użyć rozszerzenia niestandardowego skryptu platformy Azure. To rozszerzenie może wykonywać skrypty lokalnie na poszczególnych wystąpieniach maszyn wirtualnych, na przykład w celu przygotowania dołączonych dysków z danymi. Aby uzyskać więcej informacji, zobacz [Omówienie niestandardowego rozszerzenia skryptu](../virtual-machines/extensions/custom-script-windows.md).


W poniższym przykładzie za pomocą polecenia [Add-AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension) na każdym wystąpieniu maszyny wirtualnej jest wykonywany skrypt z przykładowego repozytorium GitHub, który przygotowuje wszystkie dołączone, niesformatowane dyski z danymi:


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Aby upewnić się, że dyski zostały przygotowane poprawnie, połącz się przez protokół RDP z jednym wystąpieniem maszyny wirtualnej. 

Najpierw pobierz obiekt modułu równoważenia obciążenia za pomocą polecenia [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Następnie wyświetl reguły NAT dla ruchu przychodzącego za pomocą polecenia [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig). Reguły NAT zawierają listę elementów *FrontendPort* dla poszczególnych wystąpień maszyn wirtualnych, na których nasłuchuje protokół RDP. Na koniec uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

Aby nawiązać połączenie z maszyną wirtualną, podaj swój publiczny adres IP i numer portu wymaganego wystąpienia maszyny wirtualnej, jak pokazano w poprzednich poleceniach. Po wyświetleniu monitu wprowadź poświadczenia użyte podczas tworzenia zestawu skalowania. Jeśli korzystasz z usługi Azure Cloud Shell, wykonaj ten krok przy użyciu wiersza polecenia lokalnego programu PowerShell lub klienta pulpitu zdalnego. W poniższym przykładzie nawiązano połączenie z wystąpieniem maszyny wirtualnej *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Otwórz lokalną sesję programu PowerShell na wystąpieniu maszyny wirtualnej i wyświetl informacje o dołączonych dyskach za pomocą polecenia [Get-Disk](/powershell/module/storage/get-disk):

```powershell
Get-Disk
```

Następujące przykładowe dane wyjściowe pokazują, że do wystąpienia maszyny wirtualnej są dołączone trzy dyski z danymi.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

Sprawdź systemy plików i punkty instalacji na wystąpieniu maszyny wirtualnej w następujący sposób:

```powershell
Get-Partition
```

Następujące przykładowe dane wyjściowe pokazują, że do trzech dysków z danymi przypisano litery dysku:

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

W taki sam sposób zostały przygotowane dyski na każdym wystąpieniu maszyny wirtualnej. W miarę skalowania w górę wymagane dyski z danymi są dołączane do nowych wystąpień maszyn wirtualnych. Aby automatycznie przygotować dyski, również jest uruchamiane rozszerzenie niestandardowego skryptu.

Zamknij sesję połączenia pulpitu zdalnego z wystąpieniem maszyny wirtualnej.


## <a name="list-attached-disks"></a>Wyświetlanie listy dołączonych dysków
Aby wyświetlić informacje o dyskach dołączonych do zestawu skalowania, użyj polecenia [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

Właściwość *VirtualMachineProfile.StorageProfile* zawiera listę *dysków z danymi*. Wyświetlane informacje obejmują rozmiar dysku, warstwę magazynowania i numer LUN (numer jednostki logicznej). Poniższe przykładowe dane wyjściowe zawierają szczegóły trzech dysków z danymi podłączonych do zestawu skalowania:

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>Odłączanie dysku
Jeśli dany dysk nie jest już potrzebny, można go odłączyć od zestawu skalowania. Dysk jest usuwany ze wszystkich wystąpień maszyn wirtualnych w zestawie skalowania. Aby odłączyć dysk od zestawu skalowania, użyj polecenia [Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk) i podaj numer LUN dysku. Numery LUN są widoczne w danych wyjściowych polecenia [Get-AzVmss](/powershell/module/az.compute/get-azvmss) w poprzedniej sekcji. W poniższym przykładzie przedstawiono odłączanie dysku o numerze LUN *3* od zestawu skalowania:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby pozbyć się zestawu skalowania i dysków, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Parametr `-Force` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu. Parametr `-AsJob` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono tworzenie dysków i używanie ich z zestawami skalowania za pośrednictwem programu Azure PowerShell:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski danych
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi

Przejdź do następnego samouczka, aby dowiedzieć się, jak używać niestandardowego obrazu dla wystąpień maszyn wirtualnych w zestawie skalowania.

> [!div class="nextstepaction"]
> [Use a custom image for scale set VM instances (Używanie niestandardowego obrazu dla wystąpień maszyn wirtualnych w zestawie skalowania)](tutorial-use-custom-image-powershell.md)