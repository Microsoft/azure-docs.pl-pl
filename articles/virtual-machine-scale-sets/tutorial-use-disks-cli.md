---
title: Samouczek — Tworzenie i używanie dysków dla zestawów skalowania za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć dyski funkcji Dyski zarządzane i używać ich razem z zestawem skalowania maszyn wirtualnych, na przykład dodawać, przygotowywać, wyświetlać i odłączać dyski.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 5dedee5e9ef4d036305a545201afc03d90750189
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91568322"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>Samouczek: tworzenie dysków i używanie ich z zestawem skalowania maszyn wirtualnych za pośrednictwem interfejsu wiersza polecenia platformy Azure
Zestawy skalowania maszyn wirtualnych przechowują aplikacje, dane oraz systemy operacyjne wystąpień maszyn wirtualnych na dyskach. Ważne jest, aby podczas tworzenia zestawu skalowania i zarządzania nim wybrać taki rozmiar dysku i konfigurację, które odpowiadają oczekiwanemu obciążeniu. W tym samouczku omówiono tworzenie dysków maszyn wirtualnych i zarządzanie nimi. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski danych
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.29 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).


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
W przypadku konieczności instalowania aplikacji i przechowywania danych można dodać kolejne dyski z danymi. Dyski z danymi powinny być używane w sytuacji, gdy potrzebny jest trwały i dynamiczny magazyn danych. Każdy dysk z danymi ma maksymalną pojemność wynoszącą 4 TB. Liczba dysków z danymi, które można dołączyć, zależy od rozmiaru wystąpienia maszyny wirtualnej. Dla każdego vCPU maszyny wirtualnej można dołączyć dwa dyski z danymi do bezwzględnej maksymalnie 64 dysków na maszynę wirtualną.

## <a name="vm-disk-types"></a>Typy dysków maszyny wirtualnej
Na platformie Azure dostępne są dwa typy dysków.

### <a name="standard-disk"></a>Dysk w warstwie Standardowa
Bazujący na dyskach twardych (HDD) magazyn Standard Storage stanowi ekonomiczne i wydajne rozwiązanie. Dyski w warstwie Standardowa idealnie nadają się do ekonomicznej obsługi obciążeń tworzenia i testowania.

### <a name="premium-disk"></a>Dysk w warstwie Premium
Dyski w warstwie Premium są wspierane przez oparty na technologii SSD dysk o wysokiej wydajności i niskim opóźnieniu. Te dyski są zalecane w przypadku maszyn wirtualnych, na których są uruchamiane obciążenia produkcyjne. Usługa Premium Storage obsługuje maszyny wirtualne z serii DS, DSv2, GS i FS. Podczas wybierania rozmiaru dysku wartość jest zaokrąglana do następnego typu. Jeśli na przykład rozmiar dysku wynosi mniej niż 128 GB, jest to dysk typu P10. Jeśli rozmiar dysku należy do zakresu od 129 GB do 512 GB, jest to dysk typu P20. Dyski o rozmiarze powyżej 512 GB są dyskami typu P30.

### <a name="premium-disk-performance"></a>Wydajność dysku w warstwie Premium
|Typ dysku magazynu Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Rozmiar dysku (zaokrąglony w górę) | 32 GB | 64 GB | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) |
| Maksymalna liczba operacji wejścia/wyjścia na sekundę na dysk | 120 | 240 | 500 | 2300 | 5000 | 7500 | 7500 |
Przepływność na dysk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

W powyższej tabeli podano maksymalną liczbę operacji wejścia/wyjścia na sekundę na dysk, ale wyższą wydajność można osiągnąć przez stosowanie wielu dysków z danymi. Na przykład maszyna wirtualna Standard_GS5 może osiągnąć maksymalnie 80 000 operacji we/wy na sekundę Aby uzyskać szczegółowe informacje na temat maksymalnej liczby operacji we/wy na sekundę na maszynę wirtualną, zobacz [Rozmiary maszyn wirtualnych z systemem Linux](../virtual-machines/sizes.md).


## <a name="create-and-attach-disks"></a>Tworzenie i dołączanie dysków
Dyski można tworzyć i dołączać podczas tworzenia zestawu skalowania lub w ramach modyfikacji istniejącego zestawu skalowania.

Począwszy od wersji interfejsu API `2019-07-01` , można ustawić rozmiar dysku systemu operacyjnego w zestawie skalowania maszyn wirtualnych za pomocą właściwości [obszarze storageprofile. OsDisk. diskSizeGb](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) . Po zainicjowaniu obsługi może być konieczne rozwinięcie lub ponowne partycjonowanie dysku, aby wykorzystać całe miejsce. Dowiedz się więcej [na temat rozszerzania dysku w tym miejscu](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Dołączanie dysków podczas tworzenia zestawu skalowania
Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W tym przykładzie grupa zasobów o nazwie *myResourceGroup* zostanie utworzona w regionie *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss). W poniższym przykładzie pokazano tworzenie zestawu skalowania o nazwie *myScaleSet* i generowanie kluczy SSH, jeśli nie istnieją. Dwa dyski są tworzone za pomocą parametru `--data-disk-sizes-gb`. Pierwszy dysk ma rozmiar *64* GB, a drugi — *128* GB:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania oraz wystąpień maszyn wirtualnych trwa kilka minut.

### <a name="attach-a-disk-to-existing-scale-set"></a>Dołączanie dysku do istniejącego zestawu skalowania
Dyski można także dołączać do istniejącego zestawu skalowania. Za pomocą polecenia [az vmss disk attach](/cli/azure/vmss/disk) dodaj kolejny dysk do zestawu skalowania utworzonego w poprzednim kroku. W poniższym przykładzie jest dołączany dodatkowy dysk o rozmiarze *128* GB:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>Przygotowywanie dysków z danymi
Utworzone dyski, które zostały dołączone do wystąpień maszyn wirtualnych w zestawie skalowania, są niesformatowane. Należy je przygotować, zanim będzie można ich używać z danymi i aplikacjami. Aby przygotować dyski, musisz utworzyć partycję oraz system plików oraz je zainstalować.

Aby zautomatyzować ten proces w wielu wystąpieniach maszyn wirtualnych w zestawie skalowania, możesz użyć rozszerzenia niestandardowego skryptu platformy Azure. To rozszerzenie może wykonywać skrypty lokalnie na poszczególnych wystąpieniach maszyn wirtualnych, na przykład w celu przygotowania dołączonych dysków z danymi. Aby uzyskać więcej informacji, zobacz [Omówienie niestandardowego rozszerzenia skryptu](../virtual-machines/extensions/custom-script-linux.md).

W poniższym przykładzie za pomocą polecenia [az vmss extension set](/cli/azure/vmss/extension) na każdym wystąpieniu maszyny wirtualnej jest wykonywany skrypt z przykładowego repozytorium GitHub, który przygotowuje wszystkie dołączone, niesformatowane dyski z danymi:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Aby upewnić się, że dyski zostały przygotowane poprawnie, połącz się przez protokół SSH z jednym wystąpieniem maszyny wirtualnej. Wyświetl listę informacji o połączeniu dla zestawu skalowania za pomocą polecenia [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Za pomocą własnego publicznego adresu IP i numeru portu połącz się z pierwszym wystąpieniem maszyny wirtualnej, jak pokazano w poniższym przykładzie:

```console
ssh azureuser@52.226.67.166 -p 50001
```

Sprawdź partycje na wystąpieniu maszyny wirtualnej w następujący sposób:

```bash
sudo fdisk -l
```

Następujące przykładowe dane wyjściowe pokazują, że do wystąpienia maszyny wirtualnej są dołączone trzy dyski — */dev/sdc*, */dev/sdd* i */dev/sde*. Każdy z tych dysków ma jedną partycję, która zajmuje całe dostępne miejsce:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Sprawdź systemy plików i punkty instalacji na wystąpieniu maszyny wirtualnej w następujący sposób:

```bash
sudo df -h
```

Jak widać w poniższych przykładowych danych wyjściowych systemy plików trzech dysków zostały poprawnie zainstalowane w lokalizacji */datadisks*:

```output
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

W taki sam sposób zostały przygotowane dyski na każdym wystąpieniu maszyny wirtualnej. W miarę skalowania w górę wymagane dyski z danymi są dołączane do nowych wystąpień maszyn wirtualnych. Aby automatycznie przygotować dyski, również jest uruchamiane rozszerzenie niestandardowego skryptu.

Zamknij połączenie SSH z wystąpieniem maszyny wirtualnej:

```bash
exit
```


## <a name="list-attached-disks"></a>Wyświetlanie listy dołączonych dysków
Aby wyświetlić informacje dotyczące dysków dołączonych do zestawu skalowania, użyj polecenia [az vmss show](/cli/azure/vmss) i wykonaj zapytanie na obiekcie *virtualMachineProfile.storageProfile.dataDisks*:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

Wyświetlane informacje obejmują rozmiar dysku, warstwę magazynowania i numer LUN (numer jednostki logicznej). Poniższe przykładowe dane wyjściowe zawierają szczegóły trzech dysków z danymi podłączonych do zestawu skalowania:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Odłączanie dysku
Jeśli dany dysk nie jest już potrzebny, można go odłączyć od zestawu skalowania. Dysk jest usuwany ze wszystkich wystąpień maszyn wirtualnych w zestawie skalowania. Aby odłączyć dysk od zestawu skalowania, użyj polecenia [az vmss disk detach](/cli/azure/vmss/disk) i podaj numer LUN dysku. Numery LUN są widoczne w danych wyjściowych polecenia [az vmss show](/cli/azure/vmss) w poprzedniej sekcji. W poniższym przykładzie przedstawiono odłączanie dysku o numerze LUN *2* od zestawu skalowania:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby pozbyć się zestawu skalowania i dysków, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [az group delete](/cli/azure/group). Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono tworzenie dysków i używanie ich z zestawami skalowania za pośrednictwem interfejsu wiersza polecenia platformy Azure:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski danych
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi

Przejdź do następnego samouczka, aby dowiedzieć się, jak używać niestandardowego obrazu dla wystąpień maszyn wirtualnych w zestawie skalowania.

> [!div class="nextstepaction"]
> [Use a custom image for scale set VM instances (Używanie niestandardowego obrazu dla wystąpień maszyn wirtualnych w zestawie skalowania)](tutorial-use-custom-image-cli.md)