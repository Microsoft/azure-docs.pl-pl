---
title: Problemy z pełnym dyskiem systemu operacyjnego na maszynie wirtualnej z systemem Linux
description: Jak rozwiązać problemy z pełnym dyskiem systemu operacyjnego na maszynie wirtualnej z systemem Linux
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523533"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Problemy z pełnym dyskiem systemu operacyjnego na maszynie wirtualnej z systemem Linux

Gdy dysk systemu operacyjnego maszyny wirtualnej z systemem Linux zostanie zapełniony, może to spowodować problemy z prawidłową operacją maszyny wirtualnej.

## <a name="symptom"></a>Objaw

Podczas próby utworzenia nowego pliku zostanie wyświetlony następujący komunikat:

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

Wiele demonów wskazuje, że nie mogą tworzyć plików tymczasowych podczas sesji rozruchu.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>Przyczyna

Istnieje kilka powodów, dla których może wystąpić ten komunikat o błędzie:

1. Dysk może być pełny.
1. W systemie plików mógł zostać uruchomiony program węzłów i.
1. Dysk danych może być instalowany przez istniejący katalog powodujący ukrycie plików.
1. Pliki otwierane przez proces, a następnie usuwane.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu

1. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
1. Wolne miejsce na dysku.
1. Skompiluj ponownie maszynę wirtualną.

> [!NOTE]
> W przypadku wystąpienia tego błędu system operacyjny gościa nie działa. Rozwiąż ten problem w trybie offline, aby rozwiązać ten problem.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
1. Za pomocą protokołu SSH Połącz się z maszyną wirtualną naprawy.

### <a name="free-up-space-on-the-disk"></a>Zwolnij miejsce na dysku

Aby rozwiązać ten problem:

- Zmień rozmiar dysku do 1 TB, jeśli nie jest jeszcze w maksymalnym rozmiarze wynoszącym 1 TB.
- Zwolnij miejsce na dysku.

1. Sprawdź, czy dysk jest pełny. Jeśli rozmiar dysku jest mniejszy niż 1 TB, rozwiń go maksymalnie 1 TB [przy użyciu interfejsu wiersza polecenia platformy Azure](../linux/expand-disks.md).
1. Jeśli dysk ma już 1 TB, należy zwolnić miejsce na dysku.
1. Po zmianie rozmiarów i oczyszczeniu należy wykonać ponowną kompilację maszyny wirtualnej.

### <a name="rebuild-the-vm"></a>Kompiluj ponownie maszynę wirtualną

Aby skompilować ponownie maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
