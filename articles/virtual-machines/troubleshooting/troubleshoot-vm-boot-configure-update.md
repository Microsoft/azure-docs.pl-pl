---
title: Uruchamianie maszyny wirtualnej jest zablokowane na "Trwa przygotowywanie systemu Windows. Nie wyłączaj komputera "na platformie Azure | Microsoft Docs
description: Wprowadź kroki rozwiązywania problemu z zawieszeniem uruchomienia maszyny wirtualnej "Trwa przygotowywanie systemu Windows. Nie wyłączaj komputera”.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: f3e4d51b4d41fa0dc23e9b12ac0251c14215de5c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633014"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Uruchamianie maszyny wirtualnej jest zablokowane na "Trwa przygotowywanie systemu Windows. Nie wyłączaj komputera "na platformie Azure

W tym artykule opisano ekrany "Przygotowywanie" i "Przygotowywanie systemu Windows", które mogą wystąpić podczas uruchamiania maszyny wirtualnej z systemem Windows w Microsoft Azure. Zawiera instrukcje ułatwiające zbieranie danych dla biletu pomocy technicznej.

 

## <a name="symptoms"></a>Objawy

Maszyna wirtualna z systemem Windows nie jest uruchamiana. W przypadku korzystania z **diagnostyki rozruchu** w celu pobrania zrzutu ekranu maszyny wirtualnej może pojawić się komunikat "Przygotowanie gotowe" lub "Przygotowywanie systemu Windows".

![Przykład komunikatu dla systemu Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Przykład komunikatu](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Przyczyna

Zazwyczaj ten problem występuje, gdy serwer przeprowadza ostateczną ponowny rozruch po zmianie konfiguracji. Zmiana konfiguracji może zostać zainicjowana przez aktualizacje systemu Windows lub zmiany na rolach/funkcji serwera. W przypadku Windows Update, jeśli rozmiar aktualizacji był duży, system operacyjny potrzebuje więcej czasu, aby ponownie skonfigurować zmiany.

## <a name="collect-an-os-memory-dump"></a>Zbieranie zrzutu pamięci systemu operacyjnego

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.

Jeśli problem nie zostanie rozwiązany po oczekiwaniu na zmiany w procesie, należy zebrać plik zrzutu pamięci i skontaktować się z pomocą techniczną. Aby zebrać plik zrzutu, wykonaj następujące kroki:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. Utwórz migawkę dysku systemu operacyjnego z zaatakowaną maszyną wirtualną jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).
2. [Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania](./troubleshoot-recovery-disks-portal-windows.md).
3. Pulpit zdalny do maszyny wirtualnej odzyskiwania. 
4. Jeśli dysk systemu operacyjnego jest zaszyfrowany, przed przejściem do następnego kroku należy wyłączyć szyfrowanie. Aby uzyskać więcej informacji, zobacz [odszyfrowywanie zaszyfrowanego dysku systemu operacyjnego na maszynie wirtualnej, której nie można uruchomić](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

1. Na maszynie wirtualnej odzyskiwania przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera sterownika, która jest przypisana do dołączonego dysku systemu operacyjnego, to F, należy przejść do F:\Windows.
2. Zlokalizuj plik Memory. dmp, a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu. 

Jeśli nie możesz znaleźć pliku zrzutu, przejdź do następnego kroku, aby włączyć dziennik zrzutów i konsolę seryjną.

### <a name="enable-dump-log-and-serial-console"></a>Włącz dziennik zrzutów i konsolę seryjną

Aby włączyć dziennik zrzutów i konsolę szeregową, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom poniższy skrypt:

    W tym skrypcie Załóżmy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F.  Zastąp ją odpowiednią wartością na maszynie wirtualnej.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

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

    1. Upewnij się, że na dysku jest wystarczająca ilość miejsca, aby przydzielić pamięć jako pamięć RAM, która zależy od rozmiaru wybieranego dla tej maszyny wirtualnej.
    2. Jeśli nie ma wystarczającej ilości miejsca lub jest to maszyna wirtualna o dużej wielkości (G, GS lub E), możesz zmienić lokalizację, w której ten plik zostanie utworzony, i odnieść się do dowolnego innego dysku z danymi, który jest dołączony do maszyny wirtualnej. W tym celu należy zmienić następujący klucz:
    
        ```console
        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

        reg unload HKLM\BROKENSYSTEM
        ```

3. [Odłącz dysk systemu operacyjnego, a następnie ponownie Dołącz dysk systemu operacyjnego do maszyny wirtualnej, której to dotyczy](./troubleshoot-recovery-disks-portal-windows.md).
4. Uruchom maszynę wirtualną i uzyskaj dostęp do konsoli szeregowej.
5. Wybierz pozycję **Wyślij niemaskowane przerwanie (NMI)** w celu wyzwolenia zrzutu pamięci.
    ![obraz dotyczący miejsca, w którym można wysyłać przerwanie bez maskowania](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Ponownie Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania, Zbierz plik zrzutu.

## <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Po zebraniu pliku zrzutu skontaktuj się z [pomocą techniczną firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby przeanalizować główną przyczynę.
