---
title: Odłączanie dysku danych z maszyny wirtualnej z systemem Linux — Azure
description: Dowiedz się, jak odłączyć dysk danych z maszyny wirtualnej na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 29a2cbbf2c390b81aa62b064a7cf93decbaa7457
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565992"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>How to detach a data disk from a Linux virtual machine (Jak odłączyć dysk od maszyny wirtualnej systemu Linux)

Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Spowoduje to usunięcie dysku z maszyny wirtualnej, ale nie powoduje usunięcia go z magazynu. W tym artykule pracujemy z dystrybucją Ubuntu LTS 16,04. Jeśli używasz innej dystrybucji, instrukcje dotyczące odinstalowywania dysku mogą się różnić.

> [!WARNING]
> Jeśli dysk zostanie odłączony, nie jest automatycznie usuwany. Jeśli subskrybujesz usługę Premium Storage, nadal będą naliczane opłaty za magazyn dla tego dysku. Aby uzyskać więcej informacji, zobacz [Cennik i rozliczenia w przypadku korzystania z Premium Storage](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Nawiązywanie połączenia z maszyną wirtualną w celu odinstalowania dysku

Przed odłączeniem dysku przy użyciu interfejsu wiersza polecenia lub portalu należy odinstalować dysk i usunąć odwołania do programu, jeśli plik fstab.

Nawiąż połączenie z maszyną wirtualną. W tym przykładzie publiczny adres IP maszyny wirtualnej to *10.0.1.4* z nazwą użytkownika *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Najpierw Znajdź dysk danych, który chcesz odłączyć. Poniższy przykład używa dmesg do filtrowania na dyskach SCSI:

```bash
dmesg | grep SCSI
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

W tym miejscu *SDC* to dysk, który ma zostać odłączony. Należy również uzyskać identyfikator UUID dysku.

```bash
sudo -i blkid
```

Dane wyjściowe wyglądają podobnie do poniższego przykładu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Edytuj plik */etc/fstab* , aby usunąć odwołania do tego dysku. 

> [!NOTE]
> Niewłaściwe edytowanie pliku **/etc/fstab** może spowodować, że system nie zostanie rozruchowy. Jeśli nie masz pewności, jak to zrobić, sprawdź informacje na temat prawidłowego edytowania tego pliku w dokumentacji dystrybucji. Przed rozpoczęciem edycji zaleca się również utworzenie kopii zapasowej pliku/etc/fstab.

Otwórz plik */etc/fstab* w edytorze tekstów w następujący sposób:

```bash
sudo vi /etc/fstab
```

W tym przykładzie należy usunąć następujący wiersz z pliku */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Użyj `umount` , aby odinstalować dysk. Poniższy przykład Odinstalowuje partycję */dev/sdc1* z punktu instalacji */datadrive* :

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Odłączanie dysku danych przy użyciu interfejsu wiersza polecenia platformy Azure 

Ten przykład powoduje odłączenie dysku *myDataDisk* z maszyny wirtualnej o nazwie *MyVM* w liście *zasobów*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.


## <a name="detach-a-data-disk-using-the-portal"></a>Odłączanie dysku danych przy użyciu portalu

1. W menu po lewej stronie wybierz pozycję **Virtual Machines**.
1. W bloku maszyna wirtualna wybierz pozycję **dyski**.
1. W bloku **dyski** z prawej strony dysku danych, który chcesz odłączyć, wybierz przycisk **X** , aby odłączyć dysk.
1. Po usunięciu dysku wybierz pozycję **Zapisz** w górnej części bloku.

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej. Dysk nie został usunięty.

## <a name="next-steps"></a>Następne kroki
Jeśli chcesz ponownie użyć dysku z danymi, możesz [dołączyć go do innej maszyny wirtualnej](add-disk.md).

Jeśli chcesz usunąć dysk, dzięki czemu nie będą już naliczane koszty magazynowania, zobacz [Znajdowanie i usuwanie niedołączonych dysków zarządzanych i niezarządzanych platformy Azure — Azure Portal](../disks-find-unattached-portal.md).