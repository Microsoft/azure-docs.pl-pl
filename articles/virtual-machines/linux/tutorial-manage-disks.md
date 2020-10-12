---
title: Samouczek — zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Z tego samouczka dowiesz się, jak tworzyć dyski platformy Azure dla maszyn wirtualnych i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: 948a4ae8c329d69e404ef8d0f609748b955b0ecc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078853"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Samouczek — zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Maszyny wirtualne platformy Azure przechowują swoje systemy operacyjne, aplikacje i dane na dyskach. Podczas tworzenia maszyny wirtualnej ważne jest, aby wybrać rozmiar dysku i konfigurację odpowiednie dla oczekiwanego obciążenia. Ten samouczek przedstawia sposób wdrażania dysków maszyny wirtualnej i zarządzania nimi. Zapoznasz się z informacjami na temat następujących zagadnień:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski danych
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

Na platformie Azure dostępne są dwa typy dysków.

**Dyski w warstwie Standardowa** — bazują na dyskach twardych (HDD) i stanowią ekonomiczne, chociaż wciąż wydajne rozwiązanie. Dyski w warstwie Standardowa idealnie nadają się do ekonomicznej obsługi obciążeń tworzenia i testowania.

**Dyski w warstwie Premium** — obsługiwane przez dysk SSD o wysokiej wydajności i małych opóźnieniach. Idealnie nadają się one dla maszyn wirtualnych z uruchomionym obciążeniem produkcyjnym. Rozmiary maszyn wirtualnych mające wartość  **S** w [nazwie size](../vm-naming-conventions.md)zazwyczaj obsługują Premium Storage. Na przykład maszyny wirtualne z serii DS, DSv2, GS i FS obsługują usługę Premium Storage. Podczas wybierania rozmiaru dysku wartość jest zaokrąglana do następnego typu. Na przykład jeśli rozmiar dysku jest większy niż 64 GB, ale mniejszy niż 128 GB, typ dysku to P10. 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Po udostępnieniu dysku magazynu w warstwie Premium, w przeciwieństwie do magazynu w warstwie Standardowa, gwarantujesz pojemność, liczbę operacji we/wy na sekundę oraz przepływność tego dysku. Na przykład, jeśli utworzysz dysk P50, platforma Azure ma 4 095 GB pamięci 7 500 masowej, szybkość operacji we/wy na sekundę i 250 MB/s dla tego dysku. Aplikacja może używać całości lub części pojemności i wydajności. SSD w warstwie Premium dyski zostały zaprojektowane w celu zapewnienia niskich opóźnień w milisekundach i docelowej liczby operacji we/wy i przepływności opisanej w poprzedniej tabeli o 99,9% czasu.

W powyższej tabeli podano maksymalną liczbę operacji wejścia/wyjścia na sekundę na dysk, ale wyższą wydajność można osiągnąć przez stosowanie wielu dysków z danymi. Na przykład do maszyny wirtualnej Standard_GS5 można dołączyć 64 dyski z danymi. Jeśli każdy z tych dysków ma rozmiar P30, można osiągnąć maksymalnie 80 000 operacji we/wy na sekundę. Aby uzyskać szczegółowe informacje na temat maksymalnej liczby operacji we/wy na sekundę dla maszyny wirtualnej, zobacz [VM types and sizes (Typy i rozmiary maszyn wirtualnych)](../sizes.md).

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Azure Cloud Shell to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/bash) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

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

Podziel dysk na partycje za pomocą polecenia `parted`.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

Utwórz na partycji system plików za pomocą polecenia `mkfs`. Użyj, `partprobe` Aby zapewnić, że system operacyjny wie o zmianie.

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Zainstaluj nowy dysk, aby był dostępny w systemie operacyjnym.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Dostęp do dysku można teraz uzyskać za pomocą `/datadrive` mountpoint, który można zweryfikować, uruchamiając `df -h` polecenie.

```bash
df -h | grep -i "sd"
```

W danych wyjściowych zostanie wyświetlony nowy dysk, na którym jest zainstalowany system `/datadrive` .

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Aby zapewnić, że dysk zostanie ponownie zainstalowany po ponownym uruchomieniu, należy dodać go do pliku */etc/fstab*. Aby to zrobić, uzyskaj identyfikator UUID dysku za pomocą narzędzia `blkid`.

```bash
sudo -i blkid
```

Identyfikator UUID dysku jest wyświetlany w danych wyjściowych, w tym przypadku jest to `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> Niewłaściwe edytowanie pliku **/etc/fstab** może spowodować, że system nie zostanie rozruchowy. Jeśli nie masz pewności, jak to zrobić, sprawdź informacje na temat prawidłowego edytowania tego pliku w dokumentacji dystrybucji. Przed rozpoczęciem edycji zaleca się również utworzenie kopii zapasowej pliku/etc/fstab.

Otwórz `/etc/fstab` plik w edytorze tekstów w następujący sposób:

```bash
sudo nano /etc/fstab
```

Dodaj wiersz podobny do poniższego do pliku */etc/fstab* , zastępując jego wartość UUID własnym.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

Po zakończeniu edytowania pliku Użyj polecenia, `Ctrl+O` Aby napisać plik i `Ctrl+X` zamknąć Edytor.

Teraz, gdy dysk został skonfigurowany, zamknij sesję SSH.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Zrób migawkę dysku

Utworzenie migawki dysku powoduje utworzenie przez platformę Azure jego kopii tylko do odczytu ze stałym punktem odniesienia. Migawki maszyn wirtualnych platformy Azure są przydatne do szybkiego zapisywania stanu maszyny wirtualnej przed wprowadzeniem zmian w konfiguracji. Jeśli wystąpi problem lub błąd, można przywrócić maszynę wirtualną przy użyciu migawki. Jeśli maszyna wirtualna ma więcej niż jeden dysk, migawki każdego dysku są tworzone niezależnie od innych. W przypadku wykonywania kopii zapasowych spójnych z aplikacjami rozważ zatrzymanie maszyny wirtualnej przed utworzeniem migawek dysków. Możesz także użyć [usługi Azure Backup](../../backup/index.yml), która umożliwia wykonywanie automatycznych kopii zapasowych, gdy maszyna wirtualna jest uruchomiona.

### <a name="create-snapshot"></a>Tworzenie migawki

Przed utworzeniem migawki należy potrzebować identyfikatora lub nazwy dysku. Użyj [AZ VM show](/cli/azure/vm#az-vm-show) do zrzutu identyfikatora dysku. W tym przykładzie identyfikator dysku jest przechowywany w zmiennej, aby można go było użyć w późniejszym kroku.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Teraz, gdy masz identyfikator, użyj [AZ Snapshot Create](/cli/azure/snapshot#az-snapshot-create) , aby utworzyć migawkę dysku.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Tworzenie dysku z migawki

Tę migawkę można następnie przekonwertować na dysk przy użyciu polecenia [AZ Disk Create](/cli/azure/disk#az-disk-create), którego można użyć do odtworzenia maszyny wirtualnej.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Przywracanie maszyny wirtualnej z migawki

Aby zademonstrować odzyskiwanie maszyny wirtualnej, Usuń istniejącą maszynę wirtualną za pomocą polecenia [AZ VM Delete](/cli/azure/vm#az-vm-delete).

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

Znajdź nazwę dysku danych za pomocą polecenia [AZ Disk list](/cli/azure/disk#az-disk-list) . Ten przykład umieszcza nazwę dysku w zmiennej o nazwie `datadisk` , która jest używana w następnym kroku.

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
> * Dyski danych
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi
> * Migawki dysków

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat automatyzowania konfigurowania maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Automatyzowanie konfiguracji maszyny wirtualnej](./tutorial-automate-vm-deployment.md)
