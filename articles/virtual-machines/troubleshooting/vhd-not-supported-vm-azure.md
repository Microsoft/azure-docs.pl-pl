---
title: Wirtualny dysk twardy nie jest obsługiwany podczas tworzenia maszyny wirtualnej na platformie Azure | Microsoft Docs
description: Ten artykuł pomaga skorygować błędy wirtualnego dysku twardego podczas uruchamiania maszyny wirtualnej w Microsoft Azure.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: ff4822b513ed2aea6a18ba45bffc1d060ee2410e
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937576"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>Wirtualny dysk twardy nie jest obsługiwany podczas tworzenia maszyny wirtualnej na platformie Azure

Ten artykuł pomaga poprawić błędy wirtualnego dysku twardego w przypadku uruchamiania maszyny wirtualnej w systemie Windows lub Linux.

## <a name="symptoms"></a>Objawy

Gdy tworzysz maszynę wirtualną w Microsoft Azure przy użyciu przekazanego wirtualnego dysku twardego, wdrożenie nie powiedzie się i zwróci następujący komunikat o błędzie: 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>Przyczyna

Ten problem występuje z jednego z następujących powodów:

- Wirtualny dysk twardy jest niezgodny z 1 MB wyrównaniem (przesunięciem). Obsługiwany rozmiar dysku powinien wynosić 1 MB * n. Na przykład dysk powinien mieć rozmiar 102.401 MB.
- Wirtualny dysk twardy jest uszkodzony lub nie jest obsługiwany. 

## <a name="resolution"></a>Rozwiązanie

> [!NOTE]
> Aby wykonać poniższą poprawkę, klient będzie musiał wykonać te czynności przed przekazaniem dysku VHD do platformy Azure.

Aby rozwiązać ten problem, Zmień rozmiar dysku, aby zachować zgodność z 1 MB wyrównania:

- Aby rozwiązać problem z systemem Windows, należy użyć [polecenia cmdlet programu PowerShell Zmień rozmiar dysku VHD](https://docs.microsoft.com/powershell/module/hyper-v/resize-vhd). Należy pamiętać, że **zmiana rozmiaru dysku VHD** nie jest Azure PowerShellm poleceniem cmdlet.

  1. [Zainstaluj rolę Hyper-V w systemie Windows Server](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [Konwertowanie dysku wirtualnego na wirtualny dysk twardy o stałym rozmiarze](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#convert-the-virtual-disk-to-a-fixed-size-vhd)

- Aby rozwiązać problem z systemem Linux, użyj [polecenia QEMU-IMG](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

Aby uzyskać więcej informacji na temat tworzenia i przekazywania dysku VHD do tworzenia maszyny wirtualnej platformy Azure, zobacz następujące artykuły:

- [Przekazywanie i Tworzenie maszyny wirtualnej z systemem Linux z niestandardowego obrazu dysku przy użyciu interfejsu wiersza polecenia platformy Azure 1,0](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)
- [Utwórz dysk VHD systemu Windows Server i przekaż go do platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

Dalsze problemy mogą wskazywać na uszkodzony dysk VHD. W tej sytuacji zalecamy odbudowanie wirtualnego dysku twardego od podstaw.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Informacje o wirtualnym dysku twardym systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#about-vhds)
- [Informacje o dysku VHD systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds#about-vhds)