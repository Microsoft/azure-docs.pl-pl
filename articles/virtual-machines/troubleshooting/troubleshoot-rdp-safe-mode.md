---
title: Nie można nawiązać zdalnego połączenia z platformą Azure Virtual Machines, ponieważ maszyna wirtualna jest uruchamiana w trybie awaryjnym | Microsoft Docs
description: Dowiedz się, jak rozwiązać problem polegający na tym, że nie można wykonać połączenia RDP z maszyną wirtualną, ponieważ maszyna wirtualna jest w trybie awaryjnym. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: d424cccc0a50198f3ca8c6c040afb87f44282d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508903"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nie można nawiązać połączenia RDP z maszyną wirtualną, ponieważ maszyna wirtualna jest uruchamiana w trybie awaryjnym

W tym artykule pokazano, jak rozwiązać problem polegający na tym, że nie można nawiązać połączenia z usługą Azure Windows Virtual Machines (VM), ponieważ maszyna wirtualna jest skonfigurowana tak, aby uruchamiała się w trybie awaryjnym.


## <a name="symptoms"></a>Objawy

Nie można nawiązać połączenia RDP lub innych połączeń (takich jak HTTP) z maszyną wirtualną na platformie Azure, ponieważ maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym. Po sprawdzeniu zrzutu ekranu w [diagnostyki rozruchu](../troubleshooting/boot-diagnostics.md) w Azure Portal można zobaczyć, że maszyna wirtualna jest uruchamiana normalnie, ale interfejs sieciowy nie jest dostępny:

![Obraz przedstawiający inferce sieci w trybie awaryjnym](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Przyczyna

Usługa RDP nie jest dostępna w trybie awaryjnym. Podczas uruchamiania maszyny wirtualnej w trybie awaryjnym są ładowane tylko podstawowe programy i usługi systemowe. Dotyczy to dwóch różnych wersji trybu awaryjnego, które są "Bezpieczny rozruch minimalny" i "Bezpieczny rozruch z łącznością".


## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy wykonać migawkę dysku systemu operacyjnego, którego dotyczy dana maszyna wirtualna, jako kopii zapasowej. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, należy użyć kontrolki serial, aby skonfigurować maszynę wirtualną do uruchamiania w trybie normalnym lub [naprawić maszynę wirtualną w trybie offline](#repair-the-vm-offline) przy użyciu maszyny wirtualnej odzyskiwania.

### <a name="use-serial-control"></a>Użyj kontrolki szeregowej

1. Połącz się z [konsolą szeregową i Otwórz wystąpienie programu Cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, zobacz [Naprawa maszyny wirtualnej w trybie offline](#repair-the-vm-offline).
2. Sprawdź dane konfiguracji rozruchu:

    ```console
    bcdedit /enum
    ```

    Jeśli maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym, w sekcji **modułu ładującego rozruchu systemu Windows** o nazwie **SAFEBOOT**zostanie wyświetlona dodatkowa flaga. Jeśli flaga **SAFEBOOT** nie jest widoczna, maszyna wirtualna nie jest w trybie awaryjnym. Ten artykuł nie dotyczy Twojego scenariusza.

    Flaga **SAFEBOOT** może się pojawić z następującymi wartościami:
   - Minimalny
   - Sieć

     W każdym z tych dwóch trybów protokół RDP nie zostanie uruchomiony. W związku z tym poprawka pozostaje taka sama.

     ![Obraz dotyczący flagi trybu awaryjnego](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Usuń flagę **safemoade** , aby maszyna wirtualna przejdzie w tryb normalny:

    ```console
    bcdedit /deletevalue {current} safeboot
    ```

4. Sprawdź dane konfiguracji rozruchu, aby upewnić się, że flaga **SAFEBOOT** została usunięta:

    ```console
    bcdedit /enum
    ```

5. Uruchom ponownie maszynę wirtualną, a następnie sprawdź, czy problem został rozwiązany.

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. [Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania](./troubleshoot-recovery-disks-portal-windows.md).
2. Uruchom Pulpit zdalny połączenie z maszyną wirtualną odzyskiwania.
3. Upewnij się, że dysk jest oflagowany jako **online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku przypisaną do dołączonego dysku systemu operacyjnego.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Włącz dziennik zrzutów i konsolę szeregową (opcjonalnie)

Dziennik zrzutu i konsola szeregowa mogą pomóc nam w dalszej rozwiązywaniu problemów, jeśli problem nie może zostać rozwiązany przez rozwiązanie w tym artykule.

Aby włączyć dziennik zrzutów i konsolę szeregową, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Uruchom poniższy skrypt:

    W tym skrypcie Załóżmy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zastąp tę literę dysku odpowiednią wartością dla maszyny wirtualnej.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Konfigurowanie systemu Windows w celu rozruchu w trybie normalnym

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Sprawdź dane konfiguracji rozruchu. W poniższych poleceniach przyjęto założenie, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zastąp tę literę dysku odpowiednią wartością dla maszyny wirtualnej.

    ```console
    bcdedit /store F:\boot\bcd /enum
    ```

    Zanotuj nazwę identyfikatora partycji, która ma folder **\Windows** . Domyślnie nazwa identyfikatora to "default".

    Jeśli maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym, w sekcji **modułu ładującego rozruchu systemu Windows** o nazwie **SAFEBOOT**zostanie wyświetlona dodatkowa flaga. Jeśli flaga **SAFEBOOT** nie jest widoczna, ten artykuł nie dotyczy Twojego scenariusza.

    ![Obraz przedstawiający identyfikator rozruchu](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Usuń flagę **SAFEBOOT** , aby maszyna wirtualna przejdzie w tryb normalny:

    ```console
    bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
    ```

4. Sprawdź dane konfiguracji rozruchu, aby upewnić się, że flaga **SAFEBOOT** została usunięta:

    ```console
    bcdedit /store F:\boot\bcd /enum
    ```

5. [Odłącz dysk systemu operacyjnego i Utwórz ponownie maszynę wirtualną](./troubleshoot-recovery-disks-portal-windows.md). Następnie sprawdź, czy problem został rozwiązany.
