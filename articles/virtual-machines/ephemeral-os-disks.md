---
title: Efemeryczne dyski systemu operacyjnego
description: Dowiedz się więcej o efemeralnych dyskach systemu operacyjnego dla maszyn wirtualnych platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 24b1be2ca55b057c887c8782ce7eea1150f143da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762627"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Efemeracyjne dyski systemu operacyjnego dla maszyn wirtualnych platformy Azure

Efemeracyjne dyski systemu operacyjnego są tworzone w magazynie lokalnej maszyny wirtualnej i nie są zapisywane w zdalnej usłudze Azure Storage. Efemeralne dyski systemu operacyjnego dobrze działają w przypadku obciążeń bez stanowych, w których aplikacje są tolerowane na awarie poszczególnych maszyn wirtualnych, ale mają większy wpływ na czas wdrażania maszyny wirtualnej lub ponowne odtwarzanie poszczególnych wystąpień maszyn wirtualnych. Dzięki efemernemu dyskowi systemu operacyjnego masz mniejsze opóźnienie odczytu/zapisu na dysku systemu operacyjnego i szybsze odtwarzanie maszyny wirtualnej z obrazu. 
 
Najważniejsze funkcje dysków efemeralnych to: 
- Idealne rozwiązanie w przypadku aplikacji bez stanowych.
- Można ich używać zarówno z obrazami witryny Marketplace, jak i obrazami niestandardowymi.
- Możliwość szybkiego resetowania lub ponownego tworzenia maszyn wirtualnych i wystąpień zestawu skalowania do oryginalnego stanu rozruchu.  
- Mniejsze opóźnienie, podobne do dysku tymczasowego. 
- Efemeralne dyski systemu operacyjnego są bezpłatne, nie są naliczane żadne opłaty za magazyn dla dysku systemu operacyjnego.
- Są one dostępne we wszystkich regionach świadczenia usługi Azure. 
- Efemeralny dysk systemu operacyjnego jest obsługiwany [przez](./shared-image-galleries.md)Shared Image Gallery . 
 

 
Kluczowe różnice między trwałymi i efemerskimi dyskami systemu operacyjnego:

|                             | Trwały dysk systemu operacyjnego                          | Efemeryczny dysk systemu operacyjnego                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Limit rozmiaru dysku systemu operacyjnego**      | 2 TiB                                                                                        | Rozmiar pamięci podręcznej dla rozmiaru maszyny wirtualnej lub 2TiB, w zależności od tego, który z nich jest mniejszy. Aby uzyskać **informacje o rozmiarze pamięci podręcznej w GiB**, [zobacz DS,](sizes-general.md) [ES,](sizes-memory.md) [M,](sizes-memory.md) [FS](sizes-compute.md)i [GS](sizes-previous-gen.md#gs-series)              |
| **Obsługiwane rozmiary maszyn wirtualnych**          | Wszystko                                                                                          | Rozmiary maszyn wirtualnych, które obsługują magazyn Premium, takie jak DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| **Obsługa typów dysków**           | Zarządzany i nieza zarządzany dysk systemu operacyjnego                                                                | Tylko zarządzany dysk systemu operacyjnego                                                               |
| **Obsługa regionów**              | Wszystkie regiony                                                                                  | Wszystkie regiony                              |
| **Trwałość danych**            | Dane dysku systemu operacyjnego zapisywane na dysku systemu operacyjnego są przechowywane w usłudze Azure Storage                                  | Dane zapisane na dysku systemu operacyjnego są przechowywane w lokalnym magazynie maszyny wirtualnej i nie są utrwalane w usłudze Azure Storage. |
| **Stan zatrzymania— cofniesz alokację**      | Maszyny wirtualne i wystąpienia zestawu skalowania mogą zostać zatrzymane, a ich alokacja może zostać cofzona i ponownie uruchomiona ze stanu zatrzymać— cofniesz alokację | Nie można zatrzymać/cofnić alokacji maszyn wirtualnych i wystąpień zestawu skalowania                                  |
| **Obsługa wyspecjalizowanych dysków systemu operacyjnego** | Tak                                                                                          | Nie                                                                                 |
| **Zmiana rozmiaru dysku systemu operacyjnego**              | Obsługiwane podczas tworzenia maszyny wirtualnej i po zatrzymaniu maszyny wirtualnej — cofniesz jej alokację                                | Obsługiwane tylko podczas tworzenia maszyny wirtualnej                                                  |
| **Zmiana rozmiaru na nowy rozmiar maszyny wirtualnej**   | Dane dysku systemu operacyjnego są zachowywane                                                                    | Dane na dysku systemu operacyjnego są usuwane, system operacyjny jest ponownie aprowowany       
| **Umieszczanie pliku stronicowania**   | W przypadku systemu Windows plik stronicowy jest przechowywany na dysku zasobów                                              | W przypadku systemu Windows plik stronicowy jest przechowywany na dysku systemu operacyjnego   |

## <a name="size-requirements"></a>Wymagania dotyczące rozmiaru

Możesz wdrożyć obrazy maszyn wirtualnych i wystąpień do rozmiaru pamięci podręcznej maszyny wirtualnej. Na przykład standardowe obrazy systemu Windows Server z platformy handlowej mają rozmiar około 127 GiB, co oznacza, że potrzebny jest rozmiar maszyny wirtualnej z pamięcią podręczną większą niż 127 GiB. W tym przypadku rozmiar [Standard_DS2_v2](dv2-dsv2-series.md) pamięci podręcznej wynosi 86 GiB, co nie jest wystarczająco duże. Rozmiar Standard_DS3_v2 pamięci podręcznej wynosi 172 GiB, co jest wystarczająco duże. W tym przypadku rozmiar Standard_DS3_v2 najmniejszą z serii DSv2, których można użyć z tym obrazem. Podstawowe obrazy systemu Linux w witrynie Marketplace i obrazy systemu Windows Server, które są oznaczone przez usługę , zazwyczaj mają rozmiar około 30 GiB i mogą używać większości `[smallsize]` dostępnych rozmiarów maszyn wirtualnych.

Dyski efemeracyjne wymagają również, aby rozmiar maszyny wirtualnej obsługuje magazyn w warstwie Premium. Rozmiary zwykle (ale nie zawsze) mają nazwę , na `s` przykład DSv2 i EsV3. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych platformy Azure,](sizes.md) aby uzyskać szczegółowe informacje na temat rozmiarów, które obsługują usługę Premium Storage.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Wersja zapoznawcza — efemeralne dyski systemu operacyjnego można teraz przechowywać na dyskach tymczasowych
Efemeracyjne dyski systemu operacyjnego można teraz przechowywać na dysku tymczasowym/zasobowym maszyny wirtualnej oprócz pamięci podręcznej maszyny wirtualnej. Teraz możesz używać efemeralnych dysków systemu operacyjnego z maszyną wirtualną, która nie ma pamięci podręcznej lub nie ma wystarczającej pamięci podręcznej, ale ma dysk tymczasowy/dysk zasobu do przechowywania efemerskiego dysku systemu operacyjnego, takiego jak Dav3, Dav4, Eav4 i Eav3. Jeśli maszyna wirtualna ma wystarczającą ilość pamięci podręcznej i miejsca tymczasowego, teraz będzie można również określić miejsce przechowywania efemerskiego dysku systemu operacyjnego przy użyciu nowej właściwości o nazwie [DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement). Dzięki tej funkcji podczas aprowizowania maszyny wirtualnej z systemem Windows konfigurujemy plik stronicowania tak, aby znajdował się na dysku systemu operacyjnego. Ta funkcja jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby rozpocząć, [zażądaj dostępu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u).

## <a name="powershell"></a>PowerShell

Aby użyć dysku efemery w celu wdrożenia maszyny wirtualnej programu PowerShell, użyj polecenia [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) w konfiguracji maszyny wirtualnej. Ustaw dla `-DiffDiskSetting` i `Local` wartość `-Caching` `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

W przypadku wdrożeń zestawu skalowania użyj polecenia cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) w konfiguracji. Ustaw dla `-DiffDiskSetting` i `Local` wartość `-Caching` `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Interfejs wiersza polecenia

Aby użyć dysku efemeryzowego do wdrożenia maszyny wirtualnej interfejsu wiersza polecenia, ustaw parametr w parametrze az vm create na , a `--ephemeral-os-disk` parametr na [](/cli/azure/vm#az_vm_create) `true` `--os-disk-caching` `ReadOnly` .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

W przypadku zestawów skalowania użyj tego samego `--ephemeral-os-disk true` parametru dla [parametru az-vmss-create](/cli/azure/vmss#az_vmss_create) i ustaw `--os-disk-caching` parametr na `ReadOnly` .

## <a name="portal"></a>Portal

W Azure Portal można użyć dysków efemeralnych podczas wdrażania maszyny wirtualnej, otwierając sekcję Zaawansowane **na karcie Dyski.**  W **przypadku opcji Użyj efemerowego dysku systemu operacyjnego wybierz** pozycję **Tak.**

![Zrzut ekranu przedstawiający przycisk radiowy do wybierania użycia efemeralnego dysku systemu operacyjnego](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Jeśli opcja użycia dysku efemeryzowego jest wyszarana, być może wybrano rozmiar maszyny wirtualnej, który nie ma rozmiaru pamięci podręcznej większego niż obraz systemu operacyjnego lub który nie obsługuje magazynu w chmurze Premium. Wstecz na stronę **Podstawowe** i spróbuj wybrać inny rozmiar maszyny wirtualnej.

Przy użyciu portalu można również tworzyć zestawy skalowania z efemerskimi dyskami systemu operacyjnego. Po prostu upewnij się, że wybierasz rozmiar maszyny wirtualnej z wystarczająco dużym rozmiarem pamięci podręcznej, a następnie w opcji Użyj **efemerowego** dysku systemu operacyjnego wybierz pozycję **Tak.**

![Zrzut ekranu przedstawiający przycisk radiowy do wybierania użycia efemeralnego dysku systemu operacyjnego dla zestawu skalowania](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Wdrażanie szablonu zestawu skalowania  
Proces tworzenia zestawu skalowania, który używa efemeralnego dysku systemu operacyjnego, to dodanie właściwości do typu zasobu `diffDiskSettings` `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` w szablonie. Ponadto zasady buforowania muszą być ustawione na `ReadOnly` wartość dla efemeralnego dysku systemu operacyjnego. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Wdrażanie szablonu maszyny wirtualnej 
Maszynę wirtualną z efemerskim dyskiem systemu operacyjnego można wdrożyć przy użyciu szablonu. Proces tworzenia maszyny wirtualnej, która używa efemeralnych dysków systemu operacyjnego, to dodanie właściwości do typu zasobu `diffDiskSettings` Microsoft.Compute/virtualMachines w szablonie. Ponadto zasady buforowania muszą być ustawione na `ReadOnly` wartość dla efemeralnego dysku systemu operacyjnego. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Odtwarzanie maszyny wirtualnej z obrazu przy użyciu usługi REST
Wystąpienie maszyny wirtualnej można odtąd odtąd utworzyć z efemerowym dyskiem systemu operacyjnego przy użyciu interfejsu API REST, jak opisano poniżej i za pośrednictwem witryny Azure Portal, przechodząc do okienka Przegląd maszyny wirtualnej. W przypadku zestawów skalowania odtwarzanie z obrazu jest już dostępne za pośrednictwem programu PowerShell, interfejsu wiersza polecenia i portalu.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie: jaki jest rozmiar lokalnych dysków systemu operacyjnego?**

A: Obsługujemy platformę i obrazy niestandardowe do rozmiaru pamięci podręcznej maszyny wirtualnej, gdzie wszystkie odczyty/zapis na dysku systemu operacyjnego będą lokalne w tym samym węźle co maszyna wirtualna. 

**Pytanie: Czy można zmienić rozmiar efemeralnego dysku systemu operacyjnego?**

A: Nie. Po aprowizowanych efemeralnych dyskach systemu operacyjnego nie można zmienić rozmiaru dysku systemu operacyjnego. 

**Pytanie: Czy mogę dołączyć Dyski zarządzane do efemerskiej maszyny wirtualnej?**

Odpowiedź: Tak, można dołączyć zarządzany dysk danych do maszyny wirtualnej, która korzysta z efemeralnego dysku systemu operacyjnego. 

**Pytanie: czy wszystkie rozmiary maszyn wirtualnych będą obsługiwane w przypadku efemeralnych dysków systemu operacyjnego?**

O: Nie, większość Premium Storage maszyn wirtualnych jest obsługiwana (DS, ES, FS, GS, M itp.). Aby dowiedzieć się, czy określony rozmiar maszyny wirtualnej obsługuje efemeralne dyski systemu operacyjnego, możesz:

Wywołanie polecenia `Get-AzComputeResourceSku` cmdlet programu PowerShell
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**Pytanie: Czy efemeralny dysk systemu operacyjnego można zastosować do istniejących maszyn wirtualnych i zestawów skalowania?**

O: Nie. Efemeralny dysk systemu operacyjnego może być używany tylko podczas tworzenia maszyny wirtualnej i zestawu skalowania. 

**Pytanie: Czy można mieszać efemeracyjne i normalne dyski systemu operacyjnego w zestawie skalowania?**

O: Nie, nie można mieć kombinacji efemeralnych i trwałych wystąpień dysków systemu operacyjnego w ramach tego samego zestawu skalowania. 

**Pytanie: Czy efemeralny dysk systemu operacyjnego można utworzyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia?**

Odpowiedź: Tak, maszyny wirtualne można tworzyć za pomocą efemeralnego dysku systemu operacyjnego przy użyciu interfejsu REST, szablonów, programu PowerShell i interfejsu wiersza polecenia.

**Pytanie: Jakie funkcje nie są obsługiwane w przypadku efemeralnego dysku systemu operacyjnego?**

A: Dyski efemeracyjne nie obsługują:
- Przechwytywanie obrazów maszyn wirtualnych
- Migawki dysków 
- Usługa Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Wymiana dysku systemu operacyjnego 

> [!NOTE]
> 
> Dysk efemeralny nie będzie dostępny za pośrednictwem portalu. Podczas uzyskiwania dostępu do dysku efemeralnego, który jest oczekiwany, zostanie wyświetlony błąd "Nie znaleziono zasobu" lub "404".
> 
 
## <a name="next-steps"></a>Następne kroki
Maszynę wirtualną z efemeracyjnym dyskiem systemu operacyjnego można utworzyć przy użyciu interfejsu wiersza [polecenia platformy Azure.](/cli/azure/vm#az_vm_create)
