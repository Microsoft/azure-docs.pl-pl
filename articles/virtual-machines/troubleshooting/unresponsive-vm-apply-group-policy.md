---
title: Maszyna wirtualna platformy Azure nie odpowiada podczas stosowania zasad
description: W tym artykule przedstawiono kroki rozwiązywania problemów, w których ekran ładowania nie reaguje, gdy podczas uruchamiania na maszynie wirtualnej platformy Azure zostanie zastosowane zasady.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 7160ec9564ede21eab0a205b2d66a7d566639506
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632660"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>Maszyna wirtualna nie odpowiada podczas stosowania zasady grupy zasad lokalnych użytkowników i grup

Ten artykuł zawiera kroki rozwiązywania problemów, gdy ekran ładowania nie odpowiada, gdy maszyna wirtualna platformy Azure stosuje zasady podczas uruchamiania.

## <a name="symptoms"></a>Objawy

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej ekran jest zablokowany przy użyciu komunikatu: "stosowanie zasady grupy lokalnych użytkowników i grup zasad".

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Zrzut ekranu przedstawiający zastosowanie zasad zasady grupy lokalnych użytkowników i grup (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Zrzut ekranu przedstawiający stosowanie zasad zasady grupy lokalnych użytkowników i grup (Windows Server 2012).":::

## <a name="cause"></a>Przyczyna

Istnieją blokady powodujące konflikty, gdy zasady próbują oczyścić stare profile użytkowników.

> [!NOTE]
> Dotyczy to tylko systemów Windows Server 2012 i Windows Server 2012 R2.

Oto problemy z zasadami:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.

1. [Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu](#step-1-create-and-access-a-repair-vm)
1. [Wyłączanie zasad](#step-2-disable-the-policy)
1. [Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci](#step-3-enable-serial-console-and-memory-dump-collection)
1. [Kompiluj ponownie maszynę wirtualną](#step-4-rebuild-the-vm)

> [!NOTE]
> Jeśli wystąpi ten błąd rozruchu, system operacyjny gościa nie działa. Należy rozwiązać problemy w trybie offline.

### <a name="step-1-create-and-access-a-repair-vm"></a>Krok 1. Tworzenie naprawy maszyny wirtualnej i uzyskiwanie do niej dostępu

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) , aby przygotować maszynę wirtualną naprawy.
2. Użyj Podłączanie pulpitu zdalnego, aby nawiązać połączenie z maszyną wirtualną naprawy.

### <a name="step-2-disable-the-policy"></a>Krok 2. wyłączenie zasad

1. Na stronie Naprawa maszyny wirtualnej Otwórz Edytor rejestru.
1. Znajdź klucz **HKEY_LOCAL_MACHINE** i wybierz pozycję **plik**  >  **Załaduj gałąź** z menu.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Zrzut ekranu przedstawia wyróżnione HKEY_LOCAL_MACHINE i menu zawierające gałąź ładowania.":::

    - Aby załadować klucze rejestru z systemu w trybie offline, można użyć funkcji ładowania Hive. W takim przypadku system jest uszkodzonym dyskiem podłączonym do maszyny wirtualnej naprawy.
    - Ustawienia całego systemu są przechowywane w `HKEY_LOCAL_MACHINE` i mogą być skrócone jako "HKLM".
1. Na podłączonym dysku przejdź do `\windows\system32\config\SOFTWARE` pliku i otwórz go.

    1. Gdy zostanie wyświetlony monit o podanie nazwy, wprowadź BROKENSOFTWARE.
    1. Aby sprawdzić, czy BROKENSOFTWARE został załadowany, rozwiń **HKEY_LOCAL_MACHINE** i poszukaj dodanego klucza BROKENSOFTWARE.
1. Przejdź do BROKENSOFTWARE i sprawdź, czy klucz CleanupProfile istnieje w załadowanym elemencie Hive.

    1. Jeśli klucz istnieje, zasady CleanupProfile są ustawione. Jego wartość reprezentuje zasady przechowywania mierzone w dniach. Kontynuuj usuwanie klucza.
    1. Jeśli klucz nie istnieje, zasady CleanupProfile nie są ustawione. [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), w tym plik Memory. dmp znajdujący się w katalogu systemu Windows dołączonego dysku systemu operacyjnego.

1. Usuń klucz CleanupProfiles za pomocą tego polecenia:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
1.  Zwolnij gałąź BROKENSOFTWARE za pomocą tego polecenia:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Krok 3. Włączanie zbierania danych z konsoli szeregowej i zrzutu pamięci

Aby włączyć Zbieranie zrzutów pamięci i konsolę szeregową, uruchom następujący skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień. (Uruchom jako administrator).
1. Uruchom następujące polecenia, aby włączyć konsolę seryjną:
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. Sprawdź, czy ilość wolnego miejsca na dysku systemu operacyjnego jest co najmniej równa rozmiarowi pamięci maszyny wirtualnej.

    Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, Zmień lokalizację zrzutu pamięci i zapoznaj ją z dołączonym dyskiem danych z wystarczającą ilością wolnego miejsca. Aby zmienić lokalizację, Zastąp "% główny_katalog_systemowy%" literą dysku (na przykład "F:") dysku danych w następujących poleceniach.

    **Sugerowana konfiguracja do włączenia zrzutu systemu operacyjnego**

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

### <a name="step-4-rebuild-the-vm"></a>Krok 4. ponowne kompilowanie maszyny wirtualnej

Aby ponownie połączyć maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

Jeśli problem został rozwiązany, zasady są teraz wyłączone lokalnie. W przypadku stałego rozwiązania nie należy używać zasad CleanupProfiles na maszynach wirtualnych. Użyj innej metody do przeprowadzenia czyszczenia profilów.

Nie używaj tych zasad:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z zastosowaniem Windows Update, zobacz, że [maszyna wirtualna nie odpowiada z błędem "C01A001D" podczas stosowania Windows Update](./unresponsive-vm-apply-windows-update.md).
