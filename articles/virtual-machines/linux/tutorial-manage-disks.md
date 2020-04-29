---
title: Samouczek — zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Z tego samouczka dowiesz się, jak tworzyć dyski platformy Azure dla maszyn wirtualnych i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: be2a52f1a9071e9d4bb77ab4439291fd58cd828b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82129366"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Samouczek — zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Maszyny wirtualne platformy Azure przechowują swoje systemy operacyjne, aplikacje i dane na dyskach. Podczas tworzenia maszyny wirtualnej ważne jest, aby wybrać rozmiar dysku i konfigurację odpowiednie dla oczekiwanego obciążenia. Ten samouczek przedstawia sposób wdrażania dysków maszyny wirtualnej i zarządzania nimi. Zapoznasz się z informacjami na temat następujących zagadnień:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski z danymi
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi
> * Migawki dysków


## <a name="default-azure-disks"></a>Domyślne dyski platformy Azure

Do tworzonej maszyny wirtualnej platformy Azure automatycznie dołączane są dwa dyski.

**Dysk systemu operacyjnego** — dyski systemu operacyjnego mogą mieć rozmiar do 2 TB i hostują system operacyjny maszyny wirtualnej. Domyślnie dysk sytemu operacyjnego ma etykietę */dev/sda*. Konfiguracja buforowania dysku systemu operacyjnego jest zoptymalizowana pod kątem wydajności systemu operacyjnego. Ze względu na tę konfigurację dysk sytemu operacyjnego **nie powinien** być używany przez aplikacje ani dane. Na potrzeby aplikacji i danych należy użyć dysków z danymi, które zostaną szczegółowo opisane w dalszej części tego samouczka.

**Dysk tymczasowy** — dyski tymczasowe używają dysku SSD, który znajduje się na tym samym hoście platformy Azure co maszyna wirtualna. Dyski tymczasowe są wysoce wydajne i można przy ich użyciu wykonywać operacje takie jak przetwarzanie danych tymczasowych. Jednak jeśli maszyna wirtualna zostanie przeniesiona do nowego hosta, wszystkie dane przechowywane na dysku tymczasowym zostaną usunięte. Rozmiar dysku tymczasowego zależy od rozmiaru maszyny wirtualnej. Dyski tymczasowe mają etykietę */dev/sdb* oraz punkt instalacji */mnt*.

## <a name="azure-data-disks"></a>Dyski z danymi platformy Azure

Aby zainstalować aplikacje i przechowywać dane, można dodać kolejne dyski z danymi. Dyski z danymi powinny być używane w sytuacji, gdy potrzebny jest trwały i dynamiczny magazyn danych. Liczba dysków z danymi, które można dołączyć do maszyny wirtualnej, zależy od jej rozmiaru.

## <a name="vm-disk-types"></a>Typy dysków maszyny wirtualnej

Na platformie Azure dostępne są dwa typy dysków: dyski w warstwie Standardowa i dyski w warstwie Premium.

### <a name="standard-disk"></a>Dysk w warstwie Standardowa

Magazyn Standard Storage bazuje na dyskach twardych (HDD) i stanowi ekonomiczne, chociaż wciąż wydajne rozwiązanie. Dyski w warstwie Standardowa idealnie nadają się do ekonomicznej obsługi obciążeń tworzenia i testowania.

### <a name="premium-disk"></a>Dysk w warstwie Premium

Dyski w warstwie Premium są wspierane przez oparty na technologii SSD dysk o wysokiej wydajności i niskim opóźnieniu. Idealnie nadają się one dla maszyn wirtualnych z uruchomionym obciążeniem produkcyjnym. Usługa Premium Storage obsługuje maszyny wirtualne z serii DS, DSv2, GS i FS. Podczas wybierania rozmiaru dysku wartość jest zaokrąglana do następnego typu. Jeśli na przykład rozmiar dysku wynosi mniej niż 128 GB, jest to dysk typu P10. Jeśli rozmiar dysku należy do zakresu od 129 GB do 512 GB, jest to dysk typu P20. Dyski o rozmiarze powyżej 512 GB są dyskami typu P30.

### <a name="premium-disk-performance"></a>Wydajność dysku w warstwie Premium
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

W powyższej tabeli podano maksymalną liczbę operacji wejścia/wyjścia na sekundę na dysk, ale wyższą wydajność można osiągnąć przez stosowanie wielu dysków z danymi. Na przykład maszyna wirtualna Standard_GS5 może osiągnąć maksymalnie 80 000 operacji we/wy na sekundę Aby uzyskać szczegółowe informacje na temat maksymalnej liczby operacji we/wy na sekundę na maszynę wirtualną, zobacz [Rozmiary maszyn wirtualnych z systemem Linux](sizes.md).

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Azure Cloud Shell to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/bash). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-and-attach-disks"></a>Tworzenie i dołączanie dysków

Dyski z danymi można tworzyć i dołączać podczas tworzenia maszyny wirtualnej lub dołączać je do istniejącej maszyny wirtualnej.

### <a name="attach-disk-at-vm-creation"></a>Dołączanie dysku podczas tworzenia maszyny wirtualnej

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). W poniższym przykładzie pokazano tworzenie maszyny wirtualnej o nazwie *myVM*, dodawanie konta użytkownika o nazwie *azureuser* i generowanie kluczy SSH, jeśli nie istnieją. Za pomocą argumentu `--datadisk-sizes-gb` określa się, że powinien zostać utworzony dodatkowy dysk i dołączony do maszyny wirtualnej. Aby utworzyć i dołączyć więcej niż jeden dysk, użyj rozdzielanej spacjami listy wartości rozmiarów dysków. W poniższym przykładzie maszyna wirtualna jest tworzona z dwoma dyskami z danymi o rozmiarze 128 GB każdy. Ponieważ rozmiary dysków wynoszą 128 GB, oba dyski są konfigurowane jako dyski P10, co zapewnia 500 operacji we/wy na sekundę na dysk.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Dołączanie dysku do istniejącej maszyny wirtualnej

Aby utworzyć i dołączyć nowy dysk do istniejącej maszyny wirtualnej, użyj polecenia [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). W poniższym przykładzie tworzony jest dysk w warstwie Premium o rozmiarze 128 GB i dołączany do maszyny wirtualnej utworzonej w poprzednim kroku.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Przygotowywanie dysków z danymi

Po dołączeniu dysku do maszyny wirtualnej należy skonfigurować system operacyjny do korzystania z tego dysku. Poniższy przykład pokazuje, jak ręcznie skonfigurować dysk. Ten proces można także zautomatyzować za pomocą pakietu cloud-init, co omówiono w jednym z [dalszych samouczków](./tutorial-automate-vm-deployment.md).


Utwórz połączenie SSH z maszyną wirtualną. Zastąp przykładowy adres IP publicznym adresem IP maszyny wirtualnej.

```console
ssh 10.101.10.10
```

Podziel dysk na partycje za pomocą polecenia `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Utwórz na partycji system plików za pomocą polecenia `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Zainstaluj nowy dysk, aby był dostępny w systemie operacyjnym.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Do tego dysku można teraz uzyskać dostęp za pośrednictwem punktu instalacji *datadrive*, co można zweryfikować, uruchamiając polecenie `df -h`.

```bash
df -h
```

W danych wyjściowych widoczny jest nowy dysk zainstalowany w punkcie instalacji */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Aby zapewnić, że dysk zostanie ponownie zainstalowany po ponownym uruchomieniu, należy dodać go do pliku */etc/fstab*. Aby to zrobić, uzyskaj identyfikator UUID dysku za pomocą narzędzia `blkid`.

```bash
sudo -i blkid
```

Identyfikator UUID dysku jest wyświetlany w danych wyjściowych, w tym przypadku jest to `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Dodaj do pliku */etc/fstab* wiersz podobny do następującego.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Teraz, gdy dysk został skonfigurowany, zamknij sesję SSH.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Zrób migawkę dysku

Utworzenie migawki dysku powoduje utworzenie przez platformę Azure jego kopii tylko do odczytu ze stałym punktem odniesienia. Migawki maszyn wirtualnych platformy Azure są przydatne do szybkiego zapisywania stanu maszyny wirtualnej przed wprowadzeniem zmian w konfiguracji. Jeśli wystąpi problem lub błąd, można przywrócić maszynę wirtualną przy użyciu migawki. Jeśli maszyna wirtualna ma więcej niż jeden dysk, migawki każdego dysku są tworzone niezależnie od innych. W przypadku wykonywania kopii zapasowych spójnych z aplikacjami rozważ zatrzymanie maszyny wirtualnej przed utworzeniem migawek dysków. Możesz także użyć [usługi Azure Backup](/azure/backup/), która umożliwia wykonywanie automatycznych kopii zapasowych, gdy maszyna wirtualna jest uruchomiona.

### <a name="create-snapshot"></a>Tworzenie migawki

Aby utworzyć migawkę dysku maszyny wirtualnej, należy najpierw uzyskać identyfikator i nazwę dysku. Za pomocą polecenia [az vm show](/cli/azure/vm#az-vm-show) wyświetl identyfikator dysku. W tym przykładzie identyfikator dysku jest przechowywany w zmiennej, aby można go było użyć w późniejszym kroku.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Gdy masz już identyfikator dysku maszyny wirtualnej, za pomocą poniższego polecenia możesz utworzyć migawkę tego dysku.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Tworzenie dysku z migawki

Tę migawkę można następnie przekonwertować na dysk, przy użyciu którego można odtworzyć maszynę wirtualną.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Przywracanie maszyny wirtualnej z migawki

Aby zademonstrować odzyskiwanie maszyny wirtualnej, usuń istniejącą maszynę wirtualną.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Utwórz nową maszynę wirtualną z migawki dysku.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Ponowne dołączanie dysku z danymi

Wszystkie dyski z danymi należy ponownie dołączyć do maszyny wirtualnej.

Najpierw odnajdź nazwę dysku z danymi za pomocą polecenia [az disk list](/cli/azure/disk#az-disk-list). W tym przykładzie nazwę dysku umieszczono w zmiennej o nazwie *datadisk*, która zostanie użyta w następnym kroku.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Dołącz dysk za pomocą polecenia [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach).

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące dysków maszyn wirtualnych, takie jak:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski z danymi
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi
> * Migawki dysków

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat automatyzowania konfigurowania maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Automatyzowanie konfiguracji maszyn wirtualnych](./tutorial-automate-vm-deployment.md)
