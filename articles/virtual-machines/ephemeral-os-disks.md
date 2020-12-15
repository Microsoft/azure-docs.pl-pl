---
title: Efemeryczne dyski systemu operacyjnego
description: Dowiedz się więcej o dyskach tymczasowych systemu operacyjnego dla maszyn wirtualnych platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 52071b964412071d820745b173e8835c6f9e7d0e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510995"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Tymczasowe dyski systemu operacyjnego dla maszyn wirtualnych platformy Azure

Dyski tymczasowe systemu operacyjnego są tworzone na lokalnym magazynie maszyny wirtualnej i nie zostały zapisane w zdalnym magazynie platformy Azure. Tymczasowe dyski systemu operacyjnego działają dobrze w przypadku obciążeń bezstanowych, w przypadku których aplikacje są odporne na pojedyncze awarie maszyn wirtualnych, ale mają większe wpływ na czas wdrażania maszyny wirtualnej lub odtwarzanie obrazów poszczególnych wystąpień maszyn wirtualnych. Dzięki tymczasowemu dyskowi systemu operacyjnego uzyskujesz mniejsze opóźnienie odczytu/zapisu na dysku systemu operacyjnego i szybsze odtwarzanie maszyn wirtualnych. 
 
Kluczowe funkcje dysków tymczasowych są następujące: 
- Idealne rozwiązanie w przypadku aplikacji bezstanowych.
- Mogą być używane zarówno w portalu Marketplace, jak i w obrazach niestandardowych.
- Możliwość szybkiego resetowania lub odobrazowania maszyn wirtualnych oraz wystąpień zestawów skalowania do oryginalnego stanu rozruchu.  
- Małe opóźnienia, podobne do dysku tymczasowego. 
- Dyski tymczasowe systemu operacyjnego są bezpłatne, ale nie są naliczane żadne koszty związane z magazynowaniem dysku systemu operacyjnego.
- Są one dostępne we wszystkich regionach świadczenia usługi Azure. 
- Dysk z systemem operacyjnym jest obsługiwany przez [galerię obrazów udostępnionych](./linux/shared-image-galleries.md). 
 

 
Kluczowe różnice między dyskami trwałymi i stałymi systemu operacyjnego:

|                             | Trwały dysk systemu operacyjnego                          | Efemeryczny dysk systemu operacyjnego                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Limit rozmiaru dysku systemu operacyjnego**      | 2 TiB                                                                                        | Rozmiar pamięci podręcznej dla rozmiaru maszyny wirtualnej lub 2TiB, w zależności od tego, który jest mniejszy. Dla **rozmiaru pamięci podręcznej w GIB**, zobacz [ds](sizes-general.md), [es](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md)i [GS](sizes-previous-gen.md#gs-series)              |
| **Obsługiwane rozmiary maszyn wirtualnych**          | Wszystko                                                                                          | Rozmiary maszyn wirtualnych obsługujące magazyn Premium Storage, takie jak DSv1, DSv2, DSv3, Esv3, FS, FsV2, GS, M                                               |
| **Obsługa typu dysku**           | Zarządzany i niezarządzany dysk systemu operacyjnego                                                                | Tylko zarządzany dysk systemu operacyjnego                                                               |
| **Obsługa regionów**              | Wszystkie regiony                                                                                  | Wszystkie regiony                              |
| **Trwałość danych**            | Dane dysku systemu operacyjnego zapisane na dysku systemu operacyjnego są przechowywane w usłudze Azure Storage                                  | Dane zapisane na dysku systemu operacyjnego są przechowywane w lokalnym magazynie maszyn wirtualnych i nie są utrwalane w usłudze Azure Storage. |
| **Stan zatrzymania bez przydziału**      | Wystąpienia maszyn wirtualnych i zestawów skalowania można zatrzymać bez przydziału i ponownie uruchamiać ze stanu wstrzymania bez przydziału | Nie można zatrzymać cofania przydziału maszyn wirtualnych i wystąpień zestawów skalowania                                  |
| **Wyspecjalizowana Obsługa dysków systemu operacyjnego** | Tak                                                                                          | Nie                                                                                 |
| **Zmiana rozmiaru dysku systemu operacyjnego**              | Obsługiwane podczas tworzenia maszyny wirtualnej i cofanie przydziału maszyny wirtualnej                                | Obsługiwane tylko podczas tworzenia maszyny wirtualnej                                                  |
| **Zmienianie rozmiaru do nowego rozmiaru maszyny wirtualnej**   | Dane dysku systemu operacyjnego są zachowywane                                                                    | Dane na dysku systemu operacyjnego są usuwane, ponowne Inicjowanie obsługi systemu operacyjnego       
| **Położenie pliku stronicowania**   | Dla systemu Windows, plik stronicowania jest przechowywany na dysku zasobów                                              | W przypadku systemu Windows plik stronicowania jest przechowywany na dysku systemu operacyjnego   |

## <a name="size-requirements"></a>Wymagania dotyczące rozmiaru

Możesz wdrożyć maszyny wirtualne i obrazy wystąpień o rozmiarze do rozmiaru pamięci podręcznej maszyny wirtualnej. Na przykład standardowe obrazy systemu Windows Server z portalu Marketplace dotyczą 127 GiB, co oznacza, że potrzebujesz rozmiaru maszyny wirtualnej, która ma pamięć podręczną o rozmiarze większym niż 127 GiB. W tym przypadku [Standard_DS2_v2](dv2-dsv2-series.md) ma rozmiar pamięci podręcznej wynoszącej 86 GIB, która nie jest wystarczająco duża. Standard_DS3_v2 ma rozmiar pamięci podręcznej 172 GiB, która jest wystarczająco duża. W takim przypadku Standard_DS3_v2 jest najmniejszy rozmiar w serii DSv2, którego można używać z tym obrazem. Podstawowe obrazy systemu Linux w obrazach z witryny Marketplace i Windows Server, które są ujęte w wyniku `[smallsize]` przetargu na około 30 Gib i mogą korzystać z większości dostępnych rozmiarów maszyn wirtualnych.

Dyski tymczasowe wymagają również, aby rozmiar maszyny wirtualnej obsługiwał usługę Premium Storage. Rozmiary zwykle (ale nie zawsze) mają `s` nazwę, taką jak DSv2 i EsV3. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych platformy Azure](sizes.md) , aby uzyskać szczegółowe informacje o tym, które rozmiary obsługują usługę Premium Storage.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Wersja zapoznawcza — dyski z systemem operacyjnym mogą być teraz przechowywane na dyskach tymczasowych
Tymczasowe dyski systemu operacyjnego mogą teraz być przechowywane na dysku tymczasowym/zasobów maszyny wirtualnej oprócz pamięci podręcznej maszyny wirtualnej. W związku z tym teraz można używać tymczasowych dysków systemu operacyjnego z maszyną wirtualną, która nie ma pamięci podręcznej lub ma niewystarczającą pamięć podręczną, ale ma dysk temp/Resource do przechowywania tymczasowego dysku systemu operacyjnego, takiego jak Dav3, Dav4, Eav4 i Eav3. Jeśli maszyna wirtualna ma wystarczającą ilość pamięci podręcznej i tymczasową, można teraz określić miejsce przechowywania tymczasowego dysku systemu operacyjnego przy użyciu nowej właściwości o nazwie [DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement). Po zainicjowaniu obsługi maszyny wirtualnej z systemem Windows zostanie skonfigurowany plik stronicowania, który ma znajdować się na dysku systemu operacyjnego. Ta funkcja jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby rozpocząć, [Zażądaj dostępu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u).

## <a name="powershell"></a>PowerShell

Aby użyć dysku tymczasowych do wdrożenia maszyny wirtualnej programu PowerShell, należy użyć polecenia [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) w konfiguracji maszyny wirtualnej. Ustaw wartość `-DiffDiskSetting` na `Local` i `-Caching` `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

W przypadku wdrożeń zestawu skalowania należy użyć polecenia cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) w konfiguracji. Ustaw wartość `-DiffDiskSetting` na `Local` i `-Caching` `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Interfejs wiersza polecenia

Aby użyć dysku tymczasowych do wdrożenia maszyny wirtualnej z interfejsem wiersza polecenia, należy ustawić `--ephemeral-os-disk` parametr w [AZ VM Create](/cli/azure/vm#az-vm-create) to `true` i `--os-disk-caching` parametr na `ReadOnly` .

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

Dla zestawów skalowania Użyj tego samego `--ephemeral-os-disk true` parametru dla [AZ-VMSS-Create](/cli/azure/vmss#az-vmss-create) i ustaw `--os-disk-caching` parametr na `ReadOnly` .

## <a name="portal"></a>Portal

W Azure Portal można wybrać używanie dysków tymczasowych podczas wdrażania maszyny wirtualnej, otwierając sekcję **Zaawansowane** na karcie **dyski** . W obszarze **dysk tymczasowe systemu operacyjnego** wybierz pozycję **tak**.

![Zrzut ekranu przedstawiający przycisk radiowy służący do wybierania użycia dysku z systemem operacyjnym](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Jeśli opcja korzystania z dysku tymczasowych jest wyszarzona, być może wybrano rozmiar maszyny wirtualnej, który nie ma rozmiaru pamięci podręcznej większej niż obraz systemu operacyjnego lub nie obsługuje usługi Premium Storage. Wróć do strony **podstawy** i spróbuj wybrać inny rozmiar maszyny wirtualnej.

Zestawy skalowania można również tworzyć przy użyciu tymczasowych dysków systemu operacyjnego za pomocą portalu. Upewnij się, że wybrano rozmiar maszyny wirtualnej z wystarczającą ilością pamięci podręcznej, a następnie w obszarze **Użyj dysku z systemem operacyjnym** , wybierz pozycję **tak**.

![Zrzut ekranu przedstawiający przycisk radiowy służący do wybierania użycia dysku z systemem operacyjnym w ramach zestawu skalowania](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Wdrożenie szablonu zestawu skalowania  
Proces tworzenia zestawu skalowania, który używa tymczasowych dysków systemu operacyjnego, to dodanie `diffDiskSettings` właściwości do `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` typu zasobu w szablonie. Ponadto zasady buforowania muszą być ustawione na dysk tymczasowe `ReadOnly` systemu operacyjnego. 


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

## <a name="vm-template-deployment"></a>Wdrożenie szablonu maszyny wirtualnej 
Możesz wdrożyć maszynę wirtualną z dyskiem systemu operacyjnego z systemem operacyjnym przy użyciu szablonu. Proces tworzenia maszyny wirtualnej korzystającej z dysków tymczasowych systemów operacyjnych polega na dodaniu `diffDiskSettings` właściwości do typu zasobu Microsoft. COMPUTE/virtualMachines w szablonie. Ponadto zasady buforowania muszą być ustawione na dysk tymczasowe `ReadOnly` systemu operacyjnego. 

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


## <a name="reimage-a-vm-using-rest"></a>Odtwarzanie z obrazu maszyny wirtualnej przy użyciu interfejsu REST
Można odtworzyć z obrazu wystąpienie maszyny wirtualnej z dyskiem systemu operacyjnego, korzystając z interfejsu API REST, jak opisano poniżej i za pośrednictwem witryny Azure Portal, przechodząc do okienka przegląd maszyny wirtualnej. W przypadku zestawów skalowania odtwarzanie z obrazu jest już dostępne za pomocą programu PowerShell, interfejsu wiersza polecenia i portalu.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P: Jakie są rozmiary lokalnych dysków systemu operacyjnego?**

Odp.: obsługujemy platformę i obrazy niestandardowe — do rozmiaru pamięci podręcznej maszyny wirtualnej, gdzie wszystkie operacje odczytu/zapisu na dysku systemu operacyjnego będą lokalne w tym samym węźle co maszyna wirtualna. 

**P: Czy można zmienić rozmiar dysku tymczasowych systemów operacyjnych?**

Odp.: nie, po aprowizacji tymczasowego dysku systemu operacyjnego nie można zmienić rozmiaru dysku systemu operacyjnego. 

**P: Czy można dołączyć Managed Disks do tymczasowej maszyny wirtualnej?**

Odp.: tak, można dołączyć zarządzany dysk danych do maszyny wirtualnej korzystającej z dysku z systemem operacyjnym. 

**P: czy wszystkie rozmiary maszyn wirtualnych będą obsługiwane w przypadku tymczasowych dysków systemu operacyjnego?**

Odp.: nie, obsługiwane są większość Premium Storage rozmiary maszyn wirtualnych (DS, ES, FS, GS, M itp.). Aby dowiedzieć się, czy określony rozmiar maszyny wirtualnej obsługuje dyski tymczasowe systemu operacyjnego, możesz:

Wywoływanie `Get-AzComputeResourceSku` polecenia cmdlet programu PowerShell
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
 
**P: czy dysk z systemem operacyjnym może zostać zastosowany do istniejących maszyn wirtualnych i zestawów skalowania?**

Odp.: nie, dysk z systemem operacyjnym może być używany tylko podczas tworzenia maszyny wirtualnej i zestawu skalowania. 

**P: Czy można mieszać stałe i normalne dyski systemu operacyjnego w zestawie skalowania?**

Odp.: nie, nie można utworzyć wystąpienia tymczasowych i trwałych wystąpień systemu operacyjnego w ramach tego samego zestawu skalowania. 

**P: Czy można utworzyć tymczasowych dysków systemu operacyjnego za pomocą programu PowerShell lub interfejsu wiersza polecenia?**

Odp.: tak, możesz tworzyć maszyny wirtualne z dyskiem systemu operacyjnego z systemem operacyjnym przy użyciu REST, szablonów, programu PowerShell i interfejsu wiersza polecenia.

**P: jakie funkcje nie są obsługiwane w przypadku systemu operacyjnego z systemem operacyjnym?**

Odp.: dyski tymczasowe nie obsługują:
- Przechwytywanie obrazów maszyn wirtualnych
- Migawki dysków 
- Usługa Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Wymiana dysków systemu operacyjnego 
 
## <a name="next-steps"></a>Następne kroki
Za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/vm#az-vm-create)można utworzyć maszynę wirtualną z dyskiem systemu operacyjnego.
