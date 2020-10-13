---
title: Maszyna wirtualna platformy Azure nie odpowiada z powodu błędu C01A001D podczas stosowania Windows Update
description: W tym artykule przedstawiono procedurę rozwiązywania problemów, w których usługa Windows Update generuje błąd i nie odpowiada na maszynę wirtualną platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 76c3f729a8520c7bff7b49a1d2200d7950f8a9f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074301"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Maszyna wirtualna nie odpowiada z błędem "C01A001D" podczas stosowania Windows Update

Ten artykuł zawiera kroki rozwiązywania problemów, w których Windows Update (KB) generuje błąd i nie odpowiada na maszynie wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

W przypadku wyświetlania zrzutu ekranu maszyny wirtualnej przy użyciu [diagnostyki rozruchu](./boot-diagnostics.md) jest wyświetlana Windows Update (KB) w toku, ale kończy się niepowodzeniem z kodem błędu: "C01A001D".

![nieodpowiadający Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Przyczyna

Nie można utworzyć pliku podstawowego w systemie plików. System operacyjny nie może zapisać plików na dysku.

## <a name="resolution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu

1. [Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp](#create-and-access-a-repair-vm).
2. [Zwolnij miejsce na dysku twardym](#free-up-space-on-the-hard-disk).
3. [Zalecane: przed odbudowywaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Skompiluj ponownie maszynę wirtualną](#rebuild-the-vm).

> [!NOTE]
> W przypadku wystąpienia tego błędu system operacyjny gościa nie działa. Aby rozwiązać ten problem, należy przeprowadzić rozwiązywanie problemów w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
2. Nawiąż połączenie z maszyną wirtualną naprawy przy użyciu Podłączanie pulpitu zdalnego.

### <a name="free-up-space-on-the-hard-disk"></a>Zwolnij miejsce na dysku twardym

Jeśli dysk nie jest jeszcze 1 TB, należy zmienić jego rozmiar. Po 1 TB dysku należy przeprowadzić Oczyszczanie dysku i defragmentację dysku.

1. Sprawdź, czy dysk jest pełny. Jeśli dysk jest poniżej 1 TB, [rozwiń go maksymalnie 1 TB przy użyciu programu PowerShell](../windows/expand-os-disk.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. Po 1 TB dysku należy przeprowadzić Oczyszczanie dysku.
    - [Odłącz dysk danych od uszkodzonej maszyny wirtualnej](../windows/detach-disk.md).
    - [Dołącz dysk danych do działającej maszyny wirtualnej](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
    - Za pomocą [Narzędzia do oczyszczania dysku](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) Zwolnij miejsce.
3. Po zmianie rozmiarów i oczyszczeniu należy zdefragmentować dysk:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    W zależności od poziomu fragmentacji może to potrwać kilka godzin.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Zalecane: przed odbudowywaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujące polecenia:

    Włącz konsolę seryjną:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Upewnij się, że ilość wolnego miejsca na dysku systemu operacyjnego jest co najmniej równa rozmiarowi pamięci maszyny wirtualnej (RAM).

    Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci, i odwołać go do dysku danych dołączonego do maszyny wirtualnej i wystarczającej ilości wolnego miejsca. Aby zmienić lokalizację, Zastąp `%SystemRoot%` literą dysku (na przykład "F:") dysku danych w poniższych poleceniach:

    **Włącz sugerowaną konfigurację zrzutu systemu operacyjnego:**

    Załaduj przerwany dysk systemu operacyjnego:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Włącz w ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Włącz w ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Zwolnij przerwany dysk systemu operacyjnego:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Kompiluj ponownie maszynę wirtualną

Aby ponownie połączyć maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
