---
title: Rozwiązywanie problemów z nazwami urządzeń maszyny wirtualnej z systemem Linux na platformie Azure | Microsoft Docs
description: Wyjaśnia, dlaczego nazwy urządzeń maszyn wirtualnych z systemem Linux zmieniają się i jak rozwiązać problem.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 31f64a504156134b1d622705d5301d9cd5a5f5b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88756830"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Rozwiązywanie problemów ze zmianami nazw urządzeń maszyny wirtualnej z systemem Linux

W tym artykule wyjaśniono, dlaczego nazwy urządzeń są zmieniane po ponownym uruchomieniu maszyny wirtualnej z systemem Linux lub dołączeniu dysków danych. Artykuł zawiera również rozwiązania tego problemu.

## <a name="symptoms"></a>Objawy
Podczas uruchamiania maszyn wirtualnych z systemem Linux w Microsoft Azure mogą wystąpić następujące problemy:

- Rozruch maszyny wirtualnej nie powiedzie się po ponownym uruchomieniu.
- Po odłączeniu i odłączeniu dysków danych nazwy urządzeń dyskowych są zmieniane.
- Aplikacja lub skrypt odwołujący się do dysku przy użyciu nazwy urządzenia nie powiedzie się, ponieważ zmieniono nazwę urządzenia.

## <a name="cause"></a>Przyczyna

Ścieżki urządzeń w systemie Linux nie są gwarantowane w przypadku ponownych uruchomień. Nazwy urządzeń składają się z numerów głównych (liter) i liczb pomocniczych. Gdy sterownik urządzenia magazynującego systemu Linux wykryje nowe urządzenie, sterownik przypisze do urządzenia wartości główne i pomocnicze z dostępnego zakresu. Po usunięciu urządzenia numery urządzeń są zwalniane do ponownego użycia.

Ten problem występuje, ponieważ w systemie Linux zaplanowano zaplanowanie asynchronicznego działania przez podsystem SCSI. W efekcie nazwa ścieżki urządzenia może się różnić w zależności od ponownych uruchomień.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, Użyj trwałego nazewnictwa. Istnieją cztery metody używania trwałego nazewnictwa: według etykiety systemu plików, identyfikatora UUID lub ścieżki. Zalecamy używanie etykiety systemu plików lub identyfikatora UUID dla maszyn wirtualnych z systemem Linux.

Większość dystrybucji zapewnia `fstab` parametry **nofail** lub **nobootwait** . Te parametry umożliwiają rozruch systemu w przypadku niepowodzenia instalacji dysku podczas uruchamiania. Zapoznaj się z dokumentacją dystrybucji, aby uzyskać więcej informacji o tych parametrach. Aby uzyskać informacje na temat konfigurowania maszyny wirtualnej z systemem Linux do używania identyfikatora UUID podczas dodawania dysku danych, zobacz [nawiązywanie połączenia z maszyną wirtualną z systemem Linux w celu zainstalowania nowego dysku](../linux/add-disk.md#format-and-mount-the-disk).

Gdy Agent systemu Linux platformy Azure jest zainstalowany na maszynie wirtualnej, agent używa reguł udev do konstruowania zestawu linków symbolicznych pod ścieżką/dev/Disk/Azure. Aplikacje i skrypty używają reguł udev do identyfikowania dysków dołączonych do maszyny wirtualnej, a także typu dysku i jednostek LUN dysku.

Jeśli użytkownik edytuje już fstab w taki sposób, że maszyna wirtualna nie jest uruchamiana i nie można przeprowadzić połączenia SSH z maszyną wirtualną, można użyć [konsoli szeregowej maszyny wirtualnej](./serial-console-linux.md) w celu przejścia do [trybu jednego użytkownika](./serial-console-grub-single-user-mode.md) i zmodyfikowania fstab.

### <a name="identify-disk-luns"></a>Identyfikowanie jednostek LUN dysku

Aplikacje używają jednostek LUN do znajdowania wszystkich dołączonych dysków i konstruowania linków symbolicznych. Agent systemu Azure Linux zawiera reguły udev, które konfigurują linki symboliczne z jednostki LUN do urządzeń:

```console
$ tree /dev/disk/azure

/dev/disk/azure
├── resource -> ../../sdb
├── resource-part1 -> ../../sdb1
├── root -> ../../sda
├── root-part1 -> ../../sda1
└── scsi1
    ├── lun0 -> ../../../sdc
    ├── lun0-part1 -> ../../../sdc1
    ├── lun1 -> ../../../sdd
    ├── lun1-part1 -> ../../../sdd1
    ├── lun1-part2 -> ../../../sdd2
    └── lun1-part3 -> ../../../sdd3
```

Informacje o numerze LUN z konta gościa systemu Linux są pobierane za pomocą programu `lsscsi` lub podobnego narzędzia:

```console
$ sudo lsscsi

[1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

[2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

[3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

[5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

[5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd
```

Informacje o jednostce LUN gościa są używane w metadanych subskrypcji platformy Azure w celu zlokalizowania wirtualnego dysku twardego w usłudze Azure Storage, który zawiera dane partycji. Można na przykład użyć `az` interfejsu wiersza polecenia:

```azurecli
$ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
[
{
"caching": "None",
  "createOption": "empty",
"diskSizeGb": 1023,
  "image": null,
"lun": 0,
"managedDisk": null,
"name": "testVM-20170619-114353",
"vhd": {
  "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
}
},
{
"caching": "None",
"createOption": "empty",
"diskSizeGb": 512,
"image": null,
"lun": 1,
"managedDisk": null,
"name": "testVM-20170619-121516",
"vhd": {
  "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
  }
  }
]
```

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Odnajdź Identyfikatory UUID systemu plików przy użyciu blkid

Aplikacje i skrypty odczytują dane wyjściowe `blkid` lub podobne źródła informacji, aby skonstruować linki symboliczne w ścieżce/dev. Dane wyjściowe pokazują Identyfikatory UUID wszystkich dysków dołączonych do maszyny wirtualnej i ich skojarzony plik urządzenia:

```console
$ sudo blkid -s UUID

/dev/sr0: UUID="120B021372645f72"
/dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
/dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
/dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"
```

Reguły udev agenta systemu Azure Linux konstruują zestaw linków symbolicznych w ścieżce/dev/Disk/Azure:

```console
$ ls -l /dev/disk/azure

total 0
lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1
```

Aplikacje używają linków do identyfikowania urządzenia dysku rozruchowego i dysku zasobów (tymczasowych). Na platformie Azure aplikacje powinny odszukać ścieżki/dev/Disk/Azure/root-part1 lub/dev/Disk/Azure-Resource-part1, aby odnaleźć te partycje.

Wszystkie dodatkowe partycje z `blkid` listy znajdują się na dysku z danymi. Aplikacje utrzymują identyfikator UUID dla tych partycji i używają ścieżki do odnajdywania nazwy urządzenia w czasie wykonywania:

```console
$ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1
```


### <a name="get-the-latest-azure-storage-rules"></a>Pobieranie najnowszych reguł usługi Azure Storage

Aby uzyskać najnowsze zasady usługi Azure Storage, uruchom następujące polecenia:

```console
# sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
# sudo udevadm trigger --subsystem-match=block
```

## <a name="see-also"></a>Zobacz też

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Ubuntu: używanie identyfikatora UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: trwałe nazewnictwo](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: jakie Identyfikatory UUID mogą wykonywać](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: wprowadzenie do zarządzania urządzeniami w nowoczesnych systemach Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

